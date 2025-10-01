<template>
  <div class="login-wrapper">
    <div class="login-card">
      <h2>Digaf Digital KYC!</h2>
      <p class="subtitle">Log in to continue</p>

      <input v-model="username" type="text" placeholder="Username" />
      <input v-model="password" type="password" placeholder="Password" />

      <button @click="login" class="login-btn">Login</button>

      <div class="status" :class="{ error: isError, success: isSuccess }">{{ status }}</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      username: '',
      password: '',
      status: '',
      isError: false,
      isSuccess: false
    }
  },
  methods: {
    async login() {
      if (!this.username || !this.password) {
        this.status = "Username & password required";
        this.isError = true;
        this.isSuccess = false;
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
          localStorage.setItem('username', data.username);
          localStorage.setItem('role', data.role);
          this.status = "Login successful!";
          this.isError = false;
          this.isSuccess = true;
          this.$emit('login-success');
        } else {
          this.status = data.error || "Login failed";
          this.isError = true;
          this.isSuccess = false;
        }
      } catch (err) {
        this.status = err.message;
        this.isError = true;
        this.isSuccess = false;
      }
    }
  }
}
</script>

