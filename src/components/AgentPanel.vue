<template>
  <div class="container">
    <h2>Agent Panel</h2>

    <!-- Create Video KYC Session -->
    <div class="section">
      <h3>Create Video KYC Session</h3>
      <input v-model="phone" type="text" placeholder="Customer Phone" />
      <button @click="createSession">Create Session</button>
      <p v-if="status" :style="{ color: statusColor }">{{ status }}</p>
    </div>

    <!-- Active sessions (optional) -->
    <div class="section" v-if="sessions.length">
      <h3>Active Sessions</h3>
      <table>
        <thead>
          <tr>
            <th>Customer Phone</th>
            <th>Your URL</th>
            <th>Customer URL</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(s, index) in sessions" :key="index">
            <td>{{ s.phone }}</td>
            <td><a :href="s.agentJoinUrl" target="_blank">Join</a></td>
            <td><a :href="s.customerJoinUrl" target="_blank">Customer Link</a></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  props: ['token'],
  data() {
    return {
      phone: '',
      status: '',
      statusColor: 'black',
      sessions: []
    }
  },
  methods: {
    async createSession() {
      if (!this.phone) {
        this.status = "Customer phone is required";
        this.statusColor = "red";
        return;
      }

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
          this.status = "Session created successfully!";
          this.statusColor = "green";
          this.sessions.push({
            phone: this.phone,
            agentJoinUrl: data.agentJoinUrl,
            customerJoinUrl: data.customerJoinUrl
          });
          this.phone = '';
        } else {
          this.status = "Error: " + data.error;
          this.statusColor = "red";
        }
      } catch (err) {
        this.status = "Error: " + err.message;
        this.statusColor = "red";
      }
    }
  }
}
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 40px auto;
  background: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}
h2, h3 {
  text-align: center;
  color: #333;
}
input, button {
  width: 100%;
  padding: 12px 10px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 6px;
  font-size: 16px;
}
button {
  background: #4CAF50;
  color: white;
  border: none;
  cursor: pointer;
  transition: background 0.3s;
}
button:hover { background: #45a049; }
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 15px;
}
th, td {
  padding: 10px;
  border: 1px solid #ccc;
  text-align: center;
}
th {
  background: #f4f4f4;
}
a {
  color: #007bff;
  text-decoration: none;
}
a:hover {
  text-decoration: underline;
}
.section {
  margin-bottom: 30px;
}
</style>