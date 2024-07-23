<template>
  <div class="lhep-steps">
    <div
      v-for="(branch, index) in branches"
      class="lhep-steps-branch"
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
          <lhep-branch-node
            :step="node"
            :active="active"
            :index="node.column"
            :index-text="node.indexText || node.column+1"
            :use-popo="node.usePopo"
            :branch-border-width="branchBorderWidth"
          />
        </div>
      </template>
    </div>
    <div class="lhep-steps-normal">
      <slot/>
    </div>
  </div>
</template>

<script>

import LhepBranchNode from '../LhepStep/branchNode.vue'
import eventbus from '../eventbus'

export default {
  name:"LhepSteps",
  components: {LhepBranchNode},
  props: {
    data: {
      type: Array,
      default: () => [],
      required: true
    },
    branchBorderWidth: {
      type: Number,
      default: 46
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
    crossBranchBorderWidth: {
      type: Number,
      default: 66
    }
  },
  data() {
    return {
      steps: [],
      branches: [],
      branchRects: [],
      active: 0
    }
  },
  watch: {
    steps(val) {
      val.forEach((child, index) => {
        child.index = index
        this.active = child.active
      })
    },
    data: {
      handler(val) {
        this.branches = []
        val.forEach((item, index) => {
          if (item.nodes && item.nodes.length) {
            item.nodes.forEach((node, i) => {
              node.column = index
              node.usePopo = false
              this.branches[i] = this.branches[i]? this.branches[i].concat(node): [node]
            })
          }
        })
        this.branches.reverse()
      },
      deep: true,
      immediate: true
    },
    finishColor(val) {
      this.changeColor()
    },
    activeColor(val) {
      this.changeColor()
    },
    waitColor(val) {
      this.changeColor()
    }
  },
  methods: {
    branchLocation() {
      // 计算分支节点的位置
      const doms = this.$el.querySelectorAll('.lhep-step .lhep-step__node')
      doms.forEach((dom, index) => {
        const rect = dom.getClientRects()
        this.branchRects[index] = rect[0]
      })
      const branchNodes = this.$el.querySelectorAll('.branch-node-container')
      branchNodes.forEach(dom => {
        const rect = dom.getClientRects()
        // 获取当前节点的索引（column）
        const index = dom.dataset.index
        const left = this.branchRects[index].x + this.branchRects[index].width / 2
        const rectLeft = rect[0].x + rect[0].width / 2
        if (dom.dataset.crosssteps) {
          // 跨节点分支
          const sideIndex = parseInt(index) + parseInt(dom.dataset.crosssteps)
          const sideLeft = this.branchRects[sideIndex].x + this.branchRects[sideIndex].width / 2
          const center = (sideLeft + left) / 2
          const borderWidth = center - left - rect[0].width/2 + this.crossBranchBorderWidth
          const borders = dom.querySelectorAll('.lhep-step-branch__line')
          if (borders && borders.length) {
            borders.forEach(border => {
              border.style.width = `${borderWidth}px`
            })
          }
          dom.style.transform = `translateX(${center - rectLeft - borderWidth}px)`
        } else {
          // 单节点分支
          dom.style.transform = `translateX(${left - rectLeft}px)`
        }
      })
    },
    changeColor() {
      this.$el.style.setProperty('--lhep-steps-finish-color', this.finishColor)
      this.$el.style.setProperty('--lhep-steps-process-color', this.activeColor)
      this.$el.style.setProperty('--lhep-steps-process-text-color', this.activeColor)
      this.$el.style.setProperty('--lhep-steps-wait-color', this.waitColor)
    }
  },
  created() {
    eventbus.$on('usePopo', (val, index) => {
      this.branches.forEach(b => {
        b.forEach(node => {
          if (node.column === index) {
            node.usePopo = val
          }
        })
      })
      this.$forceUpdate()
    })

    // 获取最新的active
    eventbus.$on('activeChange', (val, index) => {
      this.active = val
      this.$forceUpdate()
    })
  },
  beforeDestroy() {
    eventbus.$off('usePopo')
  },
  mounted() {
    this.branchLocation()
    this.changeColor()
  },
}

</script>

<style scoped lang='less'>
@import "../../css/lhepSteps.less";
</style>
