<template>
  <div class="container">
    <h2>Login</h2>
    <input v-model="username" type="text" placeholder="Username">
    <input v-model="password" type="password" placeholder="Password">
    <button @click="login">Login</button>
    <div id="status">{{ status }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return { username: '', password: '', status: '' }
  },
  methods: {
    async login() {
      if (!this.username || !this.password) {
        this.status = "Username & password required";
        return;
      }
      try {
        const res = await fetch('http://localhost:3000/login', {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ username: this.username, password: this.password })
        });
        const data = await res.json();
        if (data.token) {
          localStorage.setItem('token', data.token);
          localStorage.setItem('role', data.role);
          this.status = "Login successful!";
          this.$emit('login-success');
        } else {
          this.status = data.error || "Login failed";
        }
      } catch (err) {
        this.status = err.message;
      }
    }
  }
}
</script>