<script>
import LhepStepNode from './node.vue'
import {sendPopoMessage} from '../tool'
import eventbus from '../eventbus'

export default {
  name:"LhepStep",
  components: {LhepStepNode},
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
      default: () => ({}),
      required: true
    },
    indexText: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      isLast: false,
      index: -1
    };
  },
  watch: {
    usePopo(val) {
      eventbus.$emit('usePopo', val, this.index)
    },
    active(val) {
      eventbus.$emit('activeChange', val, this.index)
    }
  },
  beforeCreate() {
    this.$parent.steps.push(this);
  },
  methods: {
    sendPopo(email) {
      if (!this.usePopo) return
      window.location.href = sendPopoMessage(email);
    },
    renderSingleNode(h) {
      const title = this.$scopedSlots.title
      const description = this.$scopedSlots.description
      const head = this.$scopedSlots.head
      return h('lhep-step-node', {
        props: {
          step: this.step,
          index: this.index,
          indexText: this.indexText,
          active: this.active,
          usePopo: this.usePopo,
          isLast: this.isLast
        },
        on: {
          sendPopo: this.sendPopo
        },
        scopedSlots: {
          title: title ? props => title(props) : null,
          description: description ? props => description(props) : null,
          head: head ? props => head(props) : null
        }
      })
    }
  },
  mounted() {
    this.isLast = this.$parent.steps.indexOf(this) === this.$parent.steps.length - 1
  },
  render(h) {
    return h('div', {
      class: 'lhep-step',
    },
      [this.renderSingleNode(h)]
    )
  }
}

</script>

<style scoped lang='less'>
</style>
