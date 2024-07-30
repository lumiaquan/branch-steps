<script>
import BranchStepNode from '../branchNode/index.vue'
export default {
  name:"BrnachStep",
  components: {BranchStepNode},
  props: {
    step: {
      type: Object,
      default: () => ({}),
      required: true
    }
  },
  data() {
    return {
      isLast: false,
      index: -1,
      nameLimit: 3,
      usePopo: false,
      active: 0
    };
  },
  beforeCreate() {
    this.$parent.steps.push(this);
  },
  methods: {
    sendPopo(email) {
      if (!this.usePopo) return
      console.log(email);
      // todo 发送消息的方法
    },
    renderSingleNode(h) {
      const title = this.$scopedSlots.title
      const description = this.$scopedSlots.description
      const head = this.$scopedSlots.head
      return h('branch-step-node', {
        props: {
          step: this.step,
          index: this.index,
          active: this.active,
          usePopo: this.usePopo,
          isLast: this.isLast,
          nameLimit: this.nameLimit
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
      class: 'branch-step',
    },
      [this.renderSingleNode(h)]
    )
  }
}

</script>