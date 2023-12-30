<template>
  <div class="registration-container">
    <span>
    <h2 class="page-title">{{ editingStudent ? 'EDIT STUDENT' : 'STUDENT REGISTRATION' }}</h2>
  </span>
    <form @submit.prevent="submitForm" class="registration-form">
      <div class="form-group">
        <label for="name">NAME:</label>
        <input type="text" v-model="student.name" class="form-control" required placeholder="Enter Student Name:">
      </div>

      <div class="form-group">
        <label for="id">STUDENT ID:</label>
        <input type="text" v-model="student.Student_ID" class="form-control" required placeholder="Enter Student ID:">
      </div>

      <div class="form-group">
        <label for="department">DEPARTMENT:</label>
        <input type="text" v-model="student.Department" class="form-control" required placeholder="Enter Student Department:">
      </div>

      <div class="form-group">
        <label for="phone">PHONE:</label>
        <input type="text" v-model="student.phone" class="form-control" required placeholder="Enter Student Phone:">
      </div>

      <div class="form-group">
        <label for="sslcMark">SSLC MARK:</label>
        <input type="text" v-model="student.sslcMark" class="form-control" required placeholder="Enter SSLC Mark:">
      </div>
      <div class="form-group">
        <label for="hscMark">HSC MARK:</label>
        <input type="text" v-model="student.hscMark" class="form-control" required placeholder="Enter HSC Mark:">
      </div>

      <button type="submit" class="btn-primary">
        {{ editingStudent ? 'Update' : 'Register' }}
      </button>
    </form>

    <h2 class="page-title">REGISTERED STUDENT</h2>
    <div class="table-container">
      <table class="registered-students">
        <thead>
          <tr class = "Top_Table">
            <th>NAME</th>
            <th>STUDENT ID</th>
            <th>DEPARTMENT</th>
            <th>PHONE</th>
            <th>SSLC MARK</th>
            <th>HSC MARK</th>
            <th>ACTIONS</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="student in registeredStudents" :key="student.Student_ID">
            <td class="center-align">{{ student.name }}</td>
            <td class="center-align">{{ student.Student_ID }}</td>
            <td class="center-align">{{ student.Department }}</td>
            <td class="center-align">{{ student.phone }}</td>
            <td class="center-align">{{ student.sslcMark }}</td>
            <td class="center-align">{{ student.hscMark }}</td>
            <td class="center-align">
              <button @click="editStudent(student)" class="btn btn-warning">Edit</button>
              <button @click="deleteStudent(student.Student_ID, student.Department)" class="btn btn-danger">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      student: {
        name: '',
        Student_ID: '',
        Department: '',
        phone: '',
        sslcMark: '',
        hscMark: '',
      },
      searchId: '',
      registeredStudents: [],
      editingStudent: null,
    };
  },
  methods: {
    async submitForm() {
      try { 
        if (this.editingStudent) {
          await this.updateStudent();
        } else {
          await this.registerStudent();
        }

        await this.fetchRegisteredStudents();
        this.clearForm();
      } catch (error) {
        console.error('Error submitting form', error);
      }
    },

    async updateStudent() {
      try {
        const response = await this.$axios.put(`http://localhost:8000/api/updateStudent/${this.editingStudent.Department}/${this.editingStudent.Student_ID}`, this.student);
        console.log('Student updated successfully', response.data);
      } catch (error) {
        console.error('Error updating student', error);
      }
    },

    async registerStudent() {
      try {
        const response = await this.$axios.post('http://localhost:8000/api/register', this.student);
        console.log(this.student)
        console.log('Student registered successfully', response.data);
      } catch (error) {
        console.error('Error registering student', error);
      }
    },
    async fetchRegisteredStudents() {
      try {
        const response = await this.$axios.get('http://localhost:8000/api/registeredStudents');
        this.registeredStudents = response.data;
        console.log(response.data);
      } catch (error) {
        console.error('Error fetching registered students', error);
      }
    },

    editStudent(student) {
      this.editingStudent = { ...student };
      this.student = { ...student };
    },

    async deleteStudent(studentId, department) {
      try {
        const response = await this.$axios.delete(`http://localhost:8000/api/deleteStudent/${department}/${studentId}`);
        console.log('Student deleted successfully', response.data);
        await this.fetchRegisteredStudents();
      } catch (error) {
        console.error('Error deleting student', error);
      }
    },

    clearForm() {
      this.student = {
        name: '',
        Student_ID: '',
        Department: '',
        phone: '',
        sslcMark: '',
        hscMark: '',
      };
      this.editingStudent = null;
    },
  },
  mounted() {
    this.fetchRegisteredStudents();
  },
};
</script>

<style scoped>

.page-title {
  background-color:#4caf50;
  color:rgb(255, 255, 255);
  padding: 5px 10px;
}

.registration-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  padding: 20px;
}

.registration-form {
  max-width: 400px;
  width: 100%;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 5px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  background-color: #f5f5f5;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  color: #333;
}

.form-control {
  width: 100%;
  padding: 8px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 3px;
}

.btn {
  cursor: pointer;
  width: 100%;
  padding: 5px;
  background-color: #4caf50;
  color: white;
  font-family: 'Times New Roman', Times, serif;
  border: none;
  border-radius: 2px;
  margin-top: 5px;
  font-size:smaller;
}

.Top_Table {
  color: rgb(255, 255, 255);
  background-color:  #4caf50;
}
.table-container {
  background-color:rgb(234, 230, 226) ;
  max-height: 400px;
  overflow-y: auto;
  margin-top: 20px;
  border-color: #4caf50; /* Add margin-top to create space between the form and table */
}

.registered-students {
  width: 100%;
  text-align: center; /* Center-align table content */
  border-collapse: collapse;
}

.registered-students th, .registered-students td {
  border: 2px solid #0f0101;
  padding: 8px;
}

.center-align {
  text-align: center; /* Center-align individual table cells */
}

tbody {
  border: 1px solid;
}

.btn-primary {
  cursor: pointer;
  width: 100%;
  padding: 12px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 2px;
  font-weight:700;
  margin-top: 5px;
  font-size: 100%;
  font-family: Georgia, 'Times New Roman', Times, serif;
}
</style>
