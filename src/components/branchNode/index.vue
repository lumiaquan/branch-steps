<template>
  <div
    v-show="!step.placeholder"
    class="node"
    :class="{'wait': isWait, 'process': isProcess, 'finish': isFinish}"
  >
    <!--  并行节点左侧边框  -->
    <template v-if="isBranch">
      <!--  左侧边框  -->
      <div
        class="branch-step-branch__line left"
        :style="borderStyle"
      />
      <!--  左侧边框延长线  -->
      <div v-if="step.needAppendBorder" class="append-border left"></div>
    </template>
    <!--  左侧内容区域  -->
    <div class="branch-step__node">
      <!--   节点序号   -->
      <div class="branch-step__head">
        <slot v-if="$scopedSlots.head" name="head" :data="step"></slot>
        <div v-else class="branch-step__index">{{headContent}}</div>
      </div>
      <!--   节点标题   -->
      <div class="branch-step__title">
        <slot v-if="$scopedSlots.title" name="title" :data="step"></slot>
        <template v-else>{{step.title || ''}}</template>
      </div>
      <!--   节点描述   -->
      <div
        class="branch-step__description"
      >
        <slot v-if="$scopedSlots.description" name="description" :data="step"></slot>
        <template v-else>
          <span
            v-if="typeof step.description === 'string'"
            @click="sendPopo"
            :class="{'description__popo': usePopo}"
          >{{step.description || ''}}</span>
          <template v-else-if="Array.isArray(step.description)">
            <!--     未超过最大显示数量时     -->
            <template v-if="step.description.length<=nameLimit">
              <span v-for="(p, index) in step.description" :key="index">
                <span
                  @click="sendPopo(index)"
                  :class="{'description__popo': usePopo}"
                >
                {{p || ''}}
              </span>{{index < step.description.length - 1 ? '、' : ''}}
              </span>
            </template>
            <!--     超过最大显示数量时,hover展示全部     -->
            <el-tooltip
              v-else
              effect="dark"
              placement="top"
              popper-class="branch-step-description-tooltip"
            >
              <div class="content" slot="content">
                <span v-for="(p, index) in step.description" :key="index">
                  <span
                    @click="sendPopo(index)"
                    :class="{'description__popo': usePopo}"
                  >
                    {{p || ''}}
                  </span>{{index < step.description.length - 1 ? '、' : ''}}
                </span>
              </div>
              <div>
                <span v-for="(p, index) in step.description.slice(0, nameLimit)" :key="index">
                  <span
                    @click="sendPopo(index)"
                    :class="{'description__popo': usePopo}"
                  >
                    {{p || ''}}
                  </span>
                  {{index < nameLimit - 1 ? '、' : ''}}
                </span>等
              </div>
            </el-tooltip>
          </template>
        </template>
      </div>
    </div>
    <template v-if="isBranch">
      <!--  并行节点右侧边框  -->
      <div
        class="branch-step-branch__line right"
        :style="borderStyle"
      />
      <!--  右侧边框延长线  -->
      <div v-if="step.needAppendBorder" class="append-border right"></div>
    </template>
    <!--  右侧连接线  -->
    <div
      v-else-if="!isLast"
      class="branch-step__line"
    />
  </div>
</template>

<script>
export default {
  name:"BrnachStepNode",
  props: {
    active: {
      type: Number,
      default: 0
    },
    usePopo: {
      type: Boolean,
      default: false
    },
    step: {
      type: Object,
      default: () => ({})
    },
    index: {
      type: Number,
      default: 0
    },
    isLast: {
      type: Boolean,
      default: false
    },
    nameLimit: {
      type: Number,
      default: 3
    },
    isBranch: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {

    };
  },
  computed: {
    borderStyle() {
      if (!this.step.needAppendBorder) return {}
      return {
        'borderRight': 'none'
      }
    },
    isWait() {
      return this.step.status ? this.step.status==='wait' : this.index > this.active
    },
    isProcess() {
      return this.step.status ? this.step.status==='process' : this.index === this.active
    },
    isFinish() {
      return this.step.status ? this.step.status==='finish' : this.index < this.active
    },
    headContent() {
      if (this.isBranch) {
        return this.step.indexText || this.step.column + 1
      }
      return  this.step.indexText || this.index + 1
    }
  },
  methods: {
    sendPopo(index = null) {
      const email = typeof index === 'number' ? this.step.email[index] : this.step.email
      this.$emit('sendPopo', email)
    }
  },
  mounted() {

  },
}

</script>

<style scoped lang='less'>
@import "../../css/node.less";
</style>
