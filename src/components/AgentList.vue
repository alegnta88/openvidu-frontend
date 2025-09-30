<template>
  <div class="container">
    <h2>Agents</h2>
    <button @click="fetchAgents">Load Agents</button>
    <table v-if="agents.length">
      <thead>
        <tr>
          <th>Username</th>
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
              <input type="checkbox" :checked="agent.active" @change="toggleAgent(agent)">
              <span class="slider round"></span>
            </label>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: ['token'],
  data() {
    return { agents: [] }
  },
  methods: {
    async fetchAgents() {
      try {
        const res = await fetch('http://localhost:3000/users', {
          headers: { 'Authorization': `Bearer ${this.token}` }
        });
        const data = await res.json();
        if (data.success) {
          // Filter out admins, show only agents
          this.agents = data.users.filter(u => u.role === 'agent');
        }
      } catch (err) {
        console.error(err);
      }
    },
    async toggleAgent(agent) {
      try {
        const res = await fetch(`http://localhost:3000/users/${agent._id}/toggle`, {
          method: 'PATCH',
          headers: { 'Authorization': `Bearer ${this.token}` }
        });
        const data = await res.json();
        if (data.success) {
          agent.active = !agent.active;
        } else {
          alert(data.error);
        }
      } catch (err) {
        alert(err.message);
      }
    }
  }
}
</script>

<style>
.switch {
  position: relative;
  display: inline-block;
  width: 50px;
  height: 24px;
}
.switch input { display:none; }
.slider {
  position: absolute;
  cursor: pointer;
  top: 0; left: 0; right: 0; bottom: 0;
  background-color: #ccc;
  transition: .4s;
  border-radius: 24px;
}
.slider:before {
  position: absolute;
  content: "";
  height: 18px;
  width: 18px;
  left: 3px;
  bottom: 3px;
  background-color: white;
  transition: .4s;
  border-radius: 50%;
}
input:checked + .slider { background-color: #4CAF50; }
input:checked + .slider:before { transform: translateX(26px); }
</style>