<template>
  <div class="page">

    <div class="card form-card">
      <h2>Employee Management System</h2>

      <p v-if="message" class="alert-box">
        {{ message }}
      </p>

      <form @submit.prevent="saveEmployee">
        <input v-model="emp.name" placeholder="Employee Name" required />
        <input v-model="emp.designation" placeholder="Designation" />
        <input v-model="emp.department" placeholder="Department" />
        <input v-model="emp.salary" placeholder="Salary" />

        <button type="submit">
          {{ editMode ? "Update Employee" : "Add Employee" }}
        </button>
      </form>
    </div>

    <div class="card table-card">
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Designation</th>
            <th>Department</th>
            <th>Salary</th>
            <th>Actions</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="e in employees" :key="e.id">
            <td>{{ e.name }}</td>
            <td>{{ e.designation }}</td>
            <td>{{ e.department }}</td>
            <td>₹ {{ e.salary }}</td>
            <td>
              <button class="edit" @click="editEmployee(e)">Edit</button>
              <button class="delete" @click="deleteEmployee(e.id)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>

<script lang="ts">
import axios from "axios"

export default {
  data() {
    return {
      emp: {
        id: "",
        name: "",
        designation: "",
        department: "",
        salary: ""
      },
      employees: [] as any[],
      editMode: false,
      message: "",
      API_URL: "https://69f8bf08f7044aa0103e6c0d.mockapi.io/emp/employee"
    }
  },

  mounted() {
    this.getEmployees()
  },

  methods: {
    getEmployees() {
      axios.get(this.API_URL).then(res => {
        this.employees = res.data
      })
    },

    saveEmployee() {
      const baseUrl = this.API_URL.replace(/\/$/, "")

      const payload = {
        name: this.emp.name,
        designation: this.emp.designation,
        department: this.emp.department,
        salary: this.emp.salary
      }

      if (this.editMode && this.emp.id) {
        axios.put(`${baseUrl}/${this.emp.id}`, payload).then(() => {
          this.getEmployees()
          this.message = "Employee Updated Successfully"
          this.resetForm()
        })
      } else {
        axios.post(baseUrl, payload).then(() => {
          this.getEmployees()
          this.message = "Employee Added Successfully"
          this.resetForm()
        })
      }

      setTimeout(() => (this.message = ""), 2500)
    },

    editEmployee(e: any) {
      this.emp = { ...e }
      this.editMode = true
    },

    deleteEmployee(id: string) {
      const baseUrl = this.API_URL.replace(/\/$/, "")

      axios.delete(`${baseUrl}/${id}`).then(() => {
        this.getEmployees()
        this.message = "Employee Deleted Successfully"
      })

      setTimeout(() => (this.message = ""), 2500)
    },

    resetForm() {
      this.emp = {
        id: "",
        name: "",
        designation: "",
        department: "",
        salary: ""
      }
      this.editMode = false
    }
  }
}
</script>

<style scoped>
/* 🌈 Background */
.page {
  min-height: 100vh;
  padding: 40px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  font-family: Arial, sans-serif;
}

/* 🧊 Card Style */
.card {
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 20px;
  box-shadow: 0 8px 25px rgba(0,0,0,0.2);
  margin-bottom: 20px;
}

/* Title */
h2 {
  text-align: center;
  color: white;
  margin-bottom: 15px;
}

/* Inputs */
input {
  width: 100%;
  padding: 10px;
  margin: 6px 0;
  border: none;
  border-radius: 8px;
  outline: none;
}

/* Button */
button {
  width: 100%;
  padding: 10px;
  margin-top: 10px;
  border: none;
  border-radius: 8px;
  background: #00c6ff;
  color: white;
  font-weight: bold;
  cursor: pointer;
  transition: 0.3s;
}

button:hover {
  background: #0072ff;
  transform: scale(1.02);
}

/* Table */
table {
  width: 100%;
  border-collapse: collapse;
  color: white;
}

th, td {
  padding: 10px;
  text-align: center;
}

thead {
  background: rgba(0,0,0,0.3);
}

/* Edit/Delete buttons */
.edit {
  background: #ffc107;
  padding: 6px 10px;
  margin-right: 5px;
  border-radius: 6px;
}

.delete {
  background: #ff4d4d;
  padding: 6px 10px;
  border-radius: 6px;
}

/* Message */
.alert-box {
  text-align: center;
  background: #28a745;
  color: white;
  padding: 8px;
  border-radius: 8px;
  margin-bottom: 10px;
}
</style>