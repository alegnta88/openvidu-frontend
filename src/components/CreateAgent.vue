<template>
  <div class="container">
    <h2>Create Agent</h2>
    <input v-model="username" placeholder="Username">
    <input v-model="password" type="password" placeholder="Password">
    <button @click="createAgent">Create Agent</button>
    <div id="status">{{ status }}</div>
  </div>
</template>

<script>
export default {
  props: ['token'],
  data() {
    return { username: '', password: '', status: '' }
  },
  methods: {
    async createAgent() {
      if (!this.username || !this.password) {
        this.status = "Username & password required";
        return;
      }
      try {
        const res = await fetch('http://localhost:3000/register', {
          method: 'POST',
          headers: { 
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${this.token}`
          },
          body: JSON.stringify({ username: this.username, password: this.password, role: 'agent' })
        });
        const data = await res.json();
        if (data.success) {
          this.status = "Agent created successfully!";
          this.username = '';
          this.password = '';
          this.$emit('agent-created'); // optional event
        } else {
          this.status = "Error: " + data.error;
        }
      } catch (err) {
        this.status = "Error: " + err.message;
      }
    }
  }
}
</script>