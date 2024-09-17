<template>
  <div id="app">
    <h1>User Management</h1>

    <div>
      <h2>Add New User</h2>
      <input v-model="newUser.employeename" placeholder="Enter user name" />
      <button @click="createUser">Add User</button>
    </div>

    <div>
      <h2>User List</h2>
      <ul>
        <li v-for="user in users" :key="user.employeeid">
          <input v-model="user.employeename" />
          <button @click="updateUser(user.employeeid)">Update</button>
          <button @click="deleteUser(user.employeeid)">Delete</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      users: [],
      newUser: {
        name: ''
      }
    };
  },
  methods: {
    // Fetch all users from the backend
    async fetchUsers() {
      try {
        const response = await axios.get('http://localhost:3000/users');
        this.users = response.data;
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    },
    // Create a new user
    async createUser() {
      try {
        const response = await axios.post('http://localhost:3000/users', {
          employeename: this.newUser.employeename
        });
        this.users.push(response.data);
        this.newUser.employeename = ''; // Reset the input field
      } catch (error) {
        console.error('Error creating user:', error);
      }
    },
    // Update an existing user
    async updateUser(id) {
      const user = this.users.find(user => user.employeeid === id);
      try {
        await axios.put(`http://localhost:3000/users/${id}`, {
          employeename: user.employeename
        });
      } catch (error) {
        console.error('Error updating user:', error);
      }
    },
    // Delete a user
    async deleteUser(id) {
      try {
        await axios.delete(`http://localhost:3000/users/${id}`);
        this.users = this.users.filter(user => user.employeeid !== id);
      } catch (error) {
        console.error('Error deleting user:', error);
      }
    }
  },
  // Fetch users when the component is mounted
  mounted() {
    this.fetchUsers();
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 50px;
}

input {
  margin: 10px;
  padding: 5px;
}

button {
  margin: 5px;
  padding: 5px 10px;
  cursor: pointer;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 10px;
}
</style>
