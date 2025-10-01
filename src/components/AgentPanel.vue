<template>
  <div class="container">
    <div class="header" style="display: flex; justify-content: space-between; align-items: center;">
      <h2>Welcome, {{ username }}</h2>
      <button @click="logout" class="logout-btn">Logout</button>
    </div>

    <div class="section">
      <h3>Create Video KYC Session</h3>
      <input v-model="phone" type="text" placeholder="Enter customer phone">
      <button @click="createSession">Create Session</button>
      <div id="status">{{ status }}</div>
    </div>

    <div v-if="sessionUrls.agentJoinUrl" class="section">
      <h3>Session Links</h3>
      <p><a :href="sessionUrls.agentJoinUrl" target="_blank">Join as Agent</a></p>
      <p><a :href="sessionUrls.customerJoinUrl" target="_blank">Share with Customer</a></p>
    </div>
  </div>
</template>

<script>
export default {
  props: ['token'],
  data() {
    return {
      username: localStorage.getItem('username') || 'Agent',
      phone: '',
      status: '',
      sessionUrls: {}
    }
  },
  methods: {
    logout() {
      localStorage.removeItem('token');
      localStorage.removeItem('role');
      localStorage.removeItem('username');
      window.location.reload();
    },
    async createSession() {
      if (!this.phone.trim()) {
        this.status = "Phone is required";
        return;
      }
      this.status = "Creating session...";
      try {
        const res = await fetch('http://localhost:3000/create-session', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${this.token}`
          },
          body: JSON.stringify({ phone: this.phone })
        });
        const data = await res.json();
        if (data.success) {
          this.status = "Session created!";
          this.sessionUrls = {
            agentJoinUrl: data.agentJoinUrl,
            customerJoinUrl: data.customerJoinUrl
          };
        } else {
          this.status = data.error || "Failed to create session";
        }
      } catch (err) {
        this.status = err.message;
      }
    }
  }
}
</script>