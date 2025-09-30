<template>
  <div>
    <Login v-if="!token" @login-success="onLoginSuccess" />
    <AdminPanel v-else-if="role === 'admin'" :token="token" />
    <AgentPanel v-else-if="role === 'agent'" :token="token" />
  </div>
</template>

<script>
import Login from './components/Login.vue'
import AdminPanel from './components/AdminPanel.vue'
import AgentPanel from './components/AgentPanel.vue'

export default {
  components: { Login, AdminPanel, AgentPanel },
  data() {
    return {
      token: localStorage.getItem('token') || null,
      role: localStorage.getItem('role') || null
    }
  },
  methods: {
    onLoginSuccess() {
      this.token = localStorage.getItem('token');
      this.role = localStorage.getItem('role');
    }
  }
}
</script>