<template>
  <div class="branch-steps">
    <template v-if="branches && branches.length">
      <div
        v-for="(branch, index) in branches"
        class="branch-steps-branch"
        :class="{'no-margin-bottom': index+1===branches.length}"
        :key="index"
      >
        <template v-for="node in branch">
          <!--    分支节点（在第一列和最后一列时不显示）    -->
          <div
            v-if="node.column!==0&&node.column!==data.length-1"
            :data-index="node.column"
            :data-crossSteps="node.crossSteps"
            :key="node.column"
            class="branch-node-container"
          >
            <branch-step-node
              :step="node"
              :active="active"
              :index="node.column"
              :use-popo="usePopo"
              :name-limit="nameLimit"
              :is-branch="true"
            />
          </div>
        </template>
      </div>
    </template>
    <div class="branch-steps-normal">
      <slot/>
    </div>
  </div>
</template>

<script>

import BranchStepNode from '../branchNode/index.vue'

export default {
  name:"BranchSteps",
  components: {BranchStepNode},
  props: {
    data: {
      type: Array,
      default: () => [],
      required: true
    },
    finishColor: {
      type: String,
      default: '#90A3F4'
    },
    activeColor: {
      type: String,
      default: '#FCC702'
    },
    waitColor: {
      type: String,
      default: '#D7D9E0'
    },
    nameLimit: { // 最多显示描述文本数量
      type: Number,
      default: 3
    },
    usePopo: {
      type: Boolean,
      default: false
    },
    active: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      steps: [],
      branches: [],
      branchRects: []
    }
  },
  watch: {
    steps(val) {
      val.forEach((child, index) => {
        child.index = index
        child.nameLimit = this.nameLimit
        child.usePopo = this.usePopo
        child.active = this.active
      })
    },
    data: {
      handler(val) {
        this.branches = []
        val.forEach((item, index) => {
          if (item.nodes && item.nodes.length) {
            this.checkBorderAppend(item.nodes)
            item.nodes.forEach((node, i) => {
              node.column = index
              this.branches[i] = this.branches[i]? this.branches[i].concat(node): [node]
            })
          }
        })
        this.branches.reverse()
      },
      deep: true,
      immediate: true
    },
    active(val) {
      this.steps.forEach(step => {
        step.active = val
      })
    },
    usePopo(val) {
      this.steps.forEach(step => {
        step.usePopo = val
      })
    },
    finishColor() {
      this.changeColor()
    },
    activeColor() {
      this.changeColor()
    },
    waitColor() {
      this.changeColor()
    }
  },
  methods: {
    branchLocation() {
      // 计算分支节点的位置
      const doms = this.$el.querySelectorAll('.branch-step .branch-step__node')
      doms.forEach((dom, index) => {
        this.branchRects[index] = dom.getBoundingClientRect()
      })
      const branchNodes = this.$el.querySelectorAll('.branch-node-container')
      const baseRect = this.$el.querySelector('.branch-steps-normal').getClientRects()
      branchNodes.forEach(dom => {
        const rect = dom.getClientRects()
        // 获取当前节点的索引（column）
        const index = dom.dataset.index
        const left = this.branchRects[index].x + this.branchRects[index].width / 2
        const rectLeft = rect[0].x + rect[0].width / 2
        let borderWidth, rightBorderWidth
        if (dom.dataset.crosssteps) {
          // 跨节点分支
          const sideIndex = parseInt(index) + parseInt(dom.dataset.crosssteps)
          const sideLeft = this.branchRects[sideIndex].x + this.branchRects[sideIndex].width / 2
          const center = (sideLeft + left) / 2
          borderWidth = center - left - rect[0].width/2 + 55 + this.branchRects[index].width / 2
          rightBorderWidth = sideLeft - center - rect[0].width / 2 + 55 + this.branchRects[sideIndex].width / 2
          dom.style.transform = `translateX(${center - rectLeft - borderWidth}px)`
          this.appendBorder(dom, rect, index, baseRect)
        } else {
          // 单节点分支
          borderWidth = (this.branchRects[index].width - rect[0].width) / 2 + 55
          dom.style.transform = `translateX(${left - rectLeft - borderWidth}px)`
        }
        const borders = dom.querySelectorAll('.branch-step-branch__line')
        if (borders && borders.length) {
          borders.forEach((border, i) => {
            const width = (i === 1 && rightBorderWidth) ? rightBorderWidth : borderWidth
            border.style.width = `${width}px`
          })
        }
      })
    },
    changeColor() {
      this.$el.style.setProperty('--branch-steps-finish-color', this.finishColor)
      this.$el.style.setProperty('--branch-steps-process-color', this.activeColor)
      this.$el.style.setProperty('--branch-steps-process-text-color', this.activeColor)
      this.$el.style.setProperty('--branch-steps-wait-color', this.waitColor)
    },
    // 检查是否需要将边框延长至主流程
    checkBorderAppend(list) {
      for (let i = 1; i < list.length; i++) {
        list[i].needAppendBorder = Boolean(list[i].crossSteps)
      }
    },
    // 设置边框延长线
    appendBorder(dom, rect, i, baseRect) {
      const border = dom.querySelectorAll('.append-border')
      if (border && border.length) {
        const height = baseRect[0].y - rect[0].y
        border.forEach(item => {
          item.style.height = `${height}px`
        })
      }
    }
  },
  mounted() {
    window.requestAnimationFrame(() => {
      this.branchLocation()
      this.changeColor()
    })
  }
}

</script>

<style scoped lang='less'>
@import "../../css/branchSteps.less";
</style>

<style lang="less">
.branch-step-description-tooltip {

  .content {
    max-width: 200px;
    white-space: wrap;
    word-break: break-all;
  }

  .description__popo {
    cursor: pointer;
    &:hover {
      color: #4162E4;
      text-decoration: underline;
      text-underline-color: #4162E4;
      text-underline-offset: 3px;
    }
  }
}
</style>
