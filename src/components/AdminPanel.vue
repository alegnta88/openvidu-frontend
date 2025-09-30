<template>
  <div>
    <h1>Admin Panel</h1>
    <CreateAgent :token="token"/>
    <AgentList :token="token"/>
  </div>
</template>

<script>
import CreateAgent from './CreateAgent.vue'
import AgentList from './AgentList.vue'

export default {
  components: { CreateAgent, AgentList },
  data() {
    return { token: localStorage.getItem('token') || '' }
  },
  mounted() {
    if (!this.token) window.location.href = '/';
  }
}
</script>