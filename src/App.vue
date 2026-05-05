<template>
  <div class="container mt-4">
    <h2 class="text-center mb-4">Employee Management System</h2>

    <div class="card p-3 mb-4 shadow">
      <h4>{{ isEdit ? 'Edit Employee' : 'Add Employee' }}</h4>

      <form @submit.prevent="saveEmployee">
        <div class="row">
          <div class="col-md-6">
            <input v-model="employee.employeeId" class="form-control mb-2" placeholder="Employee ID" required />
          </div>
          <div class="col-md-6">
            <input v-model="employee.name" class="form-control mb-2" placeholder="Name" required />
          </div>
        </div>

        <div class="row">
          <div class="col-md-6">
            <input v-model="employee.designation" class="form-control mb-2" placeholder="Designation" required />
          </div>
          <div class="col-md-6">
            <input v-model="employee.department" class="form-control mb-2" placeholder="Department" required />
          </div>
        </div>

        <input v-model="employee.salary" type="number" class="form-control mb-2" placeholder="Salary" required />

        <button class="btn btn-primary me-2">
          {{ isEdit ? 'Update' : 'Add' }}
        </button>

        <button type="button" class="btn btn-secondary" @click="resetForm">
          Reset
        </button>
      </form>
    </div>

    <div class="card p-3 shadow">
      <h4>Employee List</h4>

      <table class="table table-bordered table-hover mt-3">
        <thead class="table-dark">
          <tr>
            <th>Emp ID</th>
            <th>Name</th>
            <th>Designation</th>
            <th>Department</th>
            <th>Salary</th>
            <th>Actions</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="emp in employees" :key="emp.id">
            <td>{{ emp.employeeId }}</td>
            <td>{{ emp.name }}</td>
            <td>{{ emp.designation }}</td>
            <td>{{ emp.department }}</td>
            <td>{{ emp.salary }}</td>
            <td>
              <button class="btn btn-warning btn-sm me-2" @click="editEmployee(emp)">
                Edit
              </button>
              <button class="btn btn-danger btn-sm" @click="deleteEmp(emp.id)">
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import {
  getEmployees,
  addEmployee,
  updateEmployee,
  deleteEmployee
} from './services/employeeService';

export default {
  data() {
    return {
      employees: [],
      employee: {
        employeeId: '',
        name: '',
        designation: '',
        department: '',
        salary: ''
      },
      isEdit: false,
      editId: null
    };
  },

  methods: {
    fetchEmployees() {
      getEmployees().then(res => {
        this.employees = res.data;
      });
    },

    saveEmployee() {
      if (this.isEdit) {
        updateEmployee(this.editId, this.employee).then(() => {
          this.fetchEmployees();
          this.resetForm();
        });
      } else {
        addEmployee(this.employee).then(() => {
          this.fetchEmployees();
          this.resetForm();
        });
      }
    },

    editEmployee(emp) {
      this.employee = { ...emp };
      this.isEdit = true;
      this.editId = emp.id;
    },

    deleteEmp(id) {
      if (confirm("Are you sure you want to delete?")) {
        deleteEmployee(id).then(() => {
          this.fetchEmployees();
        });
      }
    },

    resetForm() {
      this.employee = {
        employeeId: '',
        name: '',
        designation: '',
        department: '',
        salary: ''
      };
      this.isEdit = false;
      this.editId = null;
    }
  },

  mounted() {
    this.fetchEmployees();
  }
};
</script>