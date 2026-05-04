<template>
  <div class="container mt-5">
    <div class="form-card">
      <h2>Employee Management</h2>

      <p v-if="message" class="alert alert-success text-center">
        {{ message }}
      </p>

      <form @submit.prevent="saveEmployee">
        <input
          v-model="emp.name"
          placeholder="Name"
          class="form-control mb-2"
          required
        />

        <input
          v-model="emp.designation"
          placeholder="Designation"
          class="form-control mb-2"
        />

        <input
          v-model="emp.department"
          placeholder="Department"
          class="form-control mb-2"
        />

        <input
          v-model="emp.salary"
          placeholder="Salary"
          class="form-control mb-3"
        />

        <button type="submit" class="btn btn-primary w-100">
          {{ editMode ? "Update Employee" : "Add Employee" }}
        </button>
      </form>
    </div>

    <div class="table-card mt-4">
      <table class="table table-hover text-center">
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
              <button
                type="button"
                class="btn btn-warning btn-sm me-2"
                @click="editEmployee(e)"
              >
                Edit
              </button>

              <button
                type="button"
                class="btn btn-danger btn-sm"
                @click="deleteEmployee(e.id)"
              >
                Delete
              </button>
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
      API_URL: "https://69f89de5f7044aa0103e2b17.mockapi.io/emp/employees"
    }
  },

  mounted() {
    this.getEmployees()
  },

  methods: {
    getEmployees() {
      axios.get(this.API_URL)
        .then((res) => {
          this.employees = res.data
        })
        .catch((err) => {
          console.error("GET ERROR:", err)
        })
    },

    saveEmployee() {
      const payload = {
        name: this.emp.name?.trim(),
        designation: this.emp.designation?.trim(),
        department: this.emp.department?.trim(),
        salary: String(this.emp.salary ?? "").trim()
      }

      if (!payload.name) {
        this.message = "Name is required"
        return
      }

      const baseUrl = this.API_URL.replace(/\/$/, "")

      if (this.editMode && this.emp.id) {
        axios.put(${baseUrl}/${this.emp.id}, payload)
          .then((res) => {
            console.log("UPDATED:", res.data)
            this.getEmployees()
            this.message = "Employee Updated Successfully"
            this.resetForm()
          })
          .catch((err) => {
            console.error("UPDATE ERROR:", err)
          })
      } else {
        axios.post(baseUrl, payload)
          .then((res) => {
            console.log("ADDED:", res.data)
            this.getEmployees()
            this.message = "Employee Added Successfully"
            this.resetForm()
          })
          .catch((err) => {
            console.error("ADD ERROR:", err)
          })
      }

      setTimeout(() => {
        this.message = ""
      }, 3000)
    },

    editEmployee(e: any) {
      this.emp = { ...e }
      this.editMode = true
    },

    deleteEmployee(id: string) {
      const baseUrl = this.API_URL.replace(/\/$/, "")

      axios.delete(${baseUrl}/${id})
        .then(() => {
          this.getEmployees()
          this.message = "Employee Deleted Successfully"
        })
        .catch((err) => {
          console.error("DELETE ERROR:", err)
        })

      setTimeout(() => {
        this.message = ""
      }, 3000)
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
