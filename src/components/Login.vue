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

<style>
/* =====================
   Global Styles
===================== */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: linear-gradient(135deg, #2980b9, #6dd5fa, #ffffff);
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  color: #333;
}

/* =====================
   Login Card
===================== */
.login-card {
  background: #fff;
  padding: 40px;
  border-radius: 15px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
  width: 380px;
  text-align: center;
  animation: fadeIn 0.8s ease-in-out;
}

/* Logo */
.login-card img {
  width: 70px;
  margin-bottom: 15px;
}

/* Title */
.login-card h2 {
  margin-bottom: 20px;
  font-size: 26px;
  color: #2980b9;
  font-weight: 600;
}

/* Inputs */
.login-card input {
  width: 100%;
  padding: 12px 15px;
  margin-bottom: 18px;
  border-radius: 8px;
  border: 1px solid #ccc;
  outline: none;
  font-size: 16px;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.login-card input:focus {
  border-color: #2980b9;
  box-shadow: 0 0 6px rgba(41, 128, 185, 0.4);
}

/* Button */
.login-card button {
  width: 100%;
  padding: 12px;
  background: #2980b9;
  color: #fff;
  border: none;
  border-radius: 8px;
  font-size: 18px;
  cursor: pointer;
  transition: background 0.3s, transform 0.2s;
}

.login-card button:hover {
  background: #1f6691;
  transform: translateY(-2px);
}

/* Error Message */
.error-message {
  color: #e74c3c;
  margin-bottom: 12px;
  font-size: 14px;
  font-weight: bold;
}

/* =====================
   Dashboard Styles
===================== */
.dashboard {
  padding: 20px;
  text-align: center;
}

.dashboard h2 {
  color: #2980b9;
  font-size: 28px;
  margin-bottom: 15px;
}

/* =====================
   Animations
===================== */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-20px); }
  to { opacity: 1; transform: translateY(0); }
}

</style>