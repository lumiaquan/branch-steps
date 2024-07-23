<template>
  <div
    class="node"
    :class="{'wait': isWait, 'process': isProcess, 'finish': isFinish}"
  >
    <!--  左侧内容区域  -->
    <div class="lhep-step__node">
      <!--   节点序号   -->
      <div class="lhep-step__head">
        <slot v-if="$scopedSlots.head" name="head" :data="step"></slot>
        <div v-else class="lhep-step__index">
          {{indexText || index + 1}}
        </div>
      </div>
      <!--   节点标题   -->
      <div class="lhep-step__title">
        <slot v-if="$scopedSlots.title" name="title" :data="step"></slot>
        <template v-else>{{step.title || ''}}</template>
      </div>
      <!--   节点描述   -->
      <div
        class="lhep-step__description"
      >
        <slot v-if="$scopedSlots.description" name="description" :data="step"></slot>
        <template v-else>
          <span
            v-if="typeof step.description === 'string'"
            @click="sendPopo"
            :class="{'description__popo': usePopo}"
          >{{step.description || ''}}</span>
          <template v-else-if="Array.isArray(step.description)">
            <span
              v-for="(p, index) in step.description"
              @click="sendPopo(index)"
              :class="{'description__popo': usePopo}"
            >
              {{p || ''}}{{index < step.description.length - 1 ? '、' : ''}}
            </span>
          </template>
        </template>
      </div>
    </div>
    <!--  右侧连接线  -->
    <div
      v-if="!isLast"
      class="lhep-step__line"
    >
      <i class="lhep-step__line-inner"></i>
    </div>
  </div>
</template>

<script>
export default {
  name:"LhepStepNode",
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
    isLast: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {

    };
  },
  computed: {
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
