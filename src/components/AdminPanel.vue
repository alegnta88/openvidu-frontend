<template>
  <div class="container">
    <div class="header">
      <h2>Admin Panel</h2>
      <button @click="logout" class="logout-btn">Logout</button>
    </div>

    <!-- Create Agent Section -->
    <div class="section">
      <h3>Create Agent</h3>
      <input v-model="newUsername" type="text" placeholder="Username">
      <input v-model="newPassword" type="password" placeholder="Password">
      <button @click="createAgent">Create Agent</button>
      <div :style="{ color: statusColor }">{{ status }}</div>
    </div>

    <!-- Manage Agents Section -->
    <div class="section">
      <h3>Manage Agents</h3>
      <button @click="fetchAgents">Load Agents</button>
      <div :style="{ color: statusColorAgents }">{{ agentStatus }}</div>
      <table v-if="agents.length">
        <thead>
          <tr>
            <th>User Name</th>
            <th>Status</th>
            <th>Toggle</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="agent in agents" :key="agent._id">
            <td>{{ agent.username }}</td>
            <td>{{ agent.active ? 'Active' : 'Disabled' }}</td>
            <td>
              <label class="switch">
                <input type="checkbox" :checked="agent.active" @change="toggleAgent(agent._id)">
                <span class="slider round"></span>
              </label>
            </td>
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
      newUsername: '',
      newPassword: '',
      status: '',
      statusColor: 'black',
      agents: [],
      agentStatus: '',
      statusColorAgents: 'black'
    }
  },
  methods: {
    logout() {
      localStorage.removeItem('token');
      localStorage.removeItem('role');
      window.location.reload();
    },

    async createAgent() {
      if (!this.newUsername || !this.newPassword) {
        this.status = "Username & password required";
        this.statusColor = "red";
        return;
      }

      try {
        const res = await fetch('http://localhost:3000/users/register', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${this.token}`
          },
          body: JSON.stringify({
            username: this.newUsername,
            password: this.newPassword,
            role: 'agent'
          })
        });
        const data = await res.json();
        if (data.success) {
          this.status = "Agent created successfully!";
          this.statusColor = "green";
          this.newUsername = '';
          this.newPassword = '';
          this.fetchAgents();
        } else {
          this.status = data.error || "Error creating agent";
          this.statusColor = "red";
        }
      } catch (err) {
        this.status = err.message;
        this.statusColor = "red";
      }
    },

    async fetchAgents() {
      this.agentStatus = "Loading agents...";
      this.statusColorAgents = "black";

      try {
        const res = await fetch('http://localhost:3000/users', {
          headers: { 'Authorization': `Bearer ${this.token}` }
        });
        const data = await res.json();
        if (data.success) {
          // Filter only agents
          this.agents = data.users.filter(u => u.role === 'agent');
          this.agentStatus = '';
        } else {
          this.agentStatus = data.error;
          this.statusColorAgents = "red";
        }
      } catch (err) {
        this.agentStatus = err.message;
        this.statusColorAgents = "red";
      }
    },

    async toggleAgent(id) {
      try {
        const res = await fetch(`http://localhost:3000/users/${id}/toggle`, {
          method: 'PATCH',
          headers: { 'Authorization': `Bearer ${this.token}` }
        });
        const data = await res.json();
        if (data.success) {
          this.fetchAgents();
        } else {
          alert(data.error);
        }
      } catch (err) {
        alert(err.message);
      }
    }
  },

  mounted() {
    this.fetchAgents();
  }
}
</script>

