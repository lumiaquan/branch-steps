<template>
  <branch-steps
      :data="steps"
      :finish-color="finishColor"
      :wait-color="waitColor"
      :active-color="activeColor"
      :name-limit="nameLimit"
      :use-popo="usePopo"
      :active="active"
    >
      <branch-step
        v-for="(step, i) in steps"
        :index-text="step.indexText"
        :step="step"
        :key="i"
      />
      <branch-step
        :index-text="customStep.indexText"
        :step="customStep"
      >
        <template #head>
          <el-link type="primary">自定义头部</el-link>
        </template>
        <template #description>
          <el-popover
            placement="bottom"
            title="标题"
            width="200"
            trigger="click"
            content="这是一段内容,这是一段内容,这是一段内容,这是一段内容。">
            <el-button slot="reference">自定义内容</el-button>
          </el-popover>
        </template>
      </branch-step>
    </branch-steps>
</template>

<script>
import BranchSteps from './components/branchSteps'
import BranchStep from './components/branchStep'

export default {
  name: 'App',
  components: {
    BranchSteps,
    BranchStep
  },
  data() {
    return {
      active: 1,
      steps: [
        {email: 'magsik@ff.com',description:'审核人1', title: '步骤一'},
        {
          email: 'magsik@ff.com',description:'审核人2', title: '步骤二',
          nodes: [
            {email: ['magsik@ff.com','magsik@ff.com'],description:['审核人','审核人'], title: '步骤二'},
            {email: 'magsik@ff.com',description:'审核人4', title: '步骤二'},
          ]
        },
        {
          email: 'magsik@ff.com',description:'审核人5', title: '步骤三',
          nodes: [
            {placeholder: true},
            {email: 'magsik@ff.com',description:'审核人', title: '步骤五', crossSteps: 1, status: ''},
            {email: 'magsik@ff.com',description:'审核人', title: '步骤六', crossSteps: 3, status: '', indexText: '99'},
          ]
        },
        {
          email: ['magsik@ff.com','magsik@ff.com','magsik@ff.com'],
          description:['审核人','审核人','审核人'], title: '步骤七',
          nodes: [
            {email: 'magsik@ff.com',description:'审核人4', title: '步骤二'},
          ]
        },
        {email: 'magsik@ff.com',description:'审核人', title: '步骤八',
          nodes: [
            {email: 'magsik@ff.com',description:'审核人4', title: '步骤二'},
          ]
        },
        {email: ['magsik@ff.com','magsik@ff.com'],description:['审核人','审核人'], title: '步骤九' },
      ],
      customStep: {title: '自定义标题', description: '自定义内容', indexText: '99'},
      usePopo: true,
      changeColor: false,
      options: [
        { value: 'finish', label: '完成' },
        { value: 'process', label: '进行中' },
        { value: 'wait', label: '等待' }
      ],
      statusSelect: '',
      nameLimit: 2
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
