<template>
  <div
    class="node"
    :class="{'wait': isWait, 'process': isProcess, 'finish': isFinish}"
  >
    <!--  左侧边框  -->
    <div
      class="lhep-step-branch__line left"
      :style="borderStyle"
    />
    <!--  内容区域  -->
    <div class="lhep-step__node">
      <!--   节点序号   -->
      <div class="lhep-step__head">
        <div class="lhep-step__index">
          {{indexText || index + 1}}
        </div>
      </div>
      <!--   节点标题   -->
      <div class="lhep-step__title">
        <slot v-if="$scopedSlots.title" name="title" :step="step"></slot>
        <template v-else>{{step.title || ''}}</template>
      </div>
      <!--   节点描述   -->
      <div
        class="lhep-step__description"
      >
        <slot v-if="$scopedSlots.description" name="description" :step="step"></slot>
        <span
          v-else
          @click="sendPopo(step.email)"
          :class="{'description__popo': usePopo}"
        >{{step.description || ''}}</span>
      </div>
    </div>
    <!--  右侧边框  -->
    <div
      class="lhep-step-branch__line right"
      :style="borderStyle"
    />
  </div>
</template>

<script>
import {sendPopoMessage} from '../tool'

export default {
  name:"LhepBranchNode",
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
    indexText: {
      type: [String, Number],
      default: 0
    },
    branchBorderWidth: {
      type: Number,
      default: 46
    }
  },
  data() {
    return {

    };
  },
  computed: {
    borderStyle() {
      if (this.step.crossSteps) return {}
      return {
        width:`${this.branchBorderWidth}px`
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
    }
  },
  methods: {
    sendPopo(email) {
      if (!this.usePopo) return
      window.location.href = sendPopoMessage(email);
    }
  },
  mounted() {

  },
}

</script>

<style scoped lang='less'>
@import "../../css/node.less";
</style>
