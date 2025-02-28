<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";

const users = ref([]); // Store user data
const loading = ref(true);
const showModal = ref(false);
const isEditing = ref(false);
const form = ref({
  id: null,
  firstname: "",
  lastname: "",
  age: "",
  gender: "",
  interests: "",
  description: "",
});

// Fetch data from API
const fetchData = async () => {
  loading.value = true;
  try {
    const response = await axios.get("http://localhost:8000/users/");
    users.value = response.data;
  } catch (error) {
    console.error("❌ Error fetching data:", error);
  } finally {
    loading.value = false;
  }
};

// Open edit modal
const openEditModal = (user) => {
  isEditing.value = true;
  showModal.value = true;
  form.value = { ...user }; // Copy user data into form
};

// Close modal
const closeModal = () => {
  showModal.value = false;
  form.value = {
    id: null,
    firstname: "",
    lastname: "",
    age: "",
    gender: "",
    interests: "",
    description: "",
  };
};

// Add new user
const addItem = async () => {
  if (form.value.gender !== 'ชาย' && form.value.gender !== 'หญิง') {
    alert('กรุณากรอกเพศให้ถูกต้อง (ชาย/หญิง)');
    return;
  }

  if (form.value.age <= 0 || isNaN(form.value.age)) {
    alert('กรุณากรอกอายุที่ถูกต้อง (ต้องมากกว่า 0)');
    return;
  }

  if (!form.value.firstname || !form.value.lastname || !form.value.age || !form.value.gender || !form.value.interests) {
    alert('กรุณากรอกข้อมูลให้ครบทุกช่อง');
    return;
  }
  
  try {
    await axios.post("http://localhost:8000/users", form.value);
    closeModal();
    fetchData(); // Reload data
  } catch (error) {
    console.error("❌ Error adding user:", error);
  }
};

// Edit user
const editItem = async () => {
  if (form.value.gender !== 'ชาย' && form.value.gender !== 'หญิง') {
    alert('กรุณากรอกเพศให้ถูกต้อง (ชาย/หญิง)');
    return;
  }

  if (form.value.age <= 0 || isNaN(form.value.age)) {
    alert('กรุณากรอกอายุที่ถูกต้อง (ต้องมากกว่า 0)');
    return;
  }

  if (!form.value.firstname || !form.value.lastname || !form.value.age || !form.value.gender || !form.value.interests) {
    alert('กรุณากรอกข้อมูลให้ครบทุกช่อง');
    return;
  }
  
  try {
    await axios.put(`http://localhost:8000/users/${form.value.id}`, form.value);
    closeModal();
    fetchData(); // Reload data
  } catch (error) {
    console.error("❌ Error editing user:", error);
  }
};

// Delete user
const deleteItem = async (id) => {
  if (!confirm("คุณแน่ใจหรือไม่ว่าต้องการลบผู้ใช้นี้?")) return;
  try {
    await axios.delete(`http://localhost:8000/users/${id}`);
    fetchData(); // Reload data
  } catch (error) {
    console.error("❌ Error deleting user:", error);
  }
};

onMounted(fetchData);
</script>

<template>
    
  <div class="container">
    <header class="header">
      <h1 class="title">Users List</h1>
      <p class="subtitle">จัดการข้อมูลผู้ใช้ในระบบ</p>
    </header>

    <div v-if="loading" class="loading">⏳ Loading...</div>

    <table v-else class="user-table">
      <thead>
        <tr>
          <th>Id</th>
          <th>FirstName</th>
          <th>LastName</th>
          <th>Age</th>
          <th>Gender</th>
          <th>Interests</th>
          <th>Description</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="user in users" :key="user.id">
          <td>{{ user.id }}</td>
          <td>{{ user.firstname }}</td>
          <td>{{ user.lastname }}</td>
          <td>{{ user.age }}</td>
          <td>{{ user.gender }}</td>
          <td>{{ user.interests }}</td>
          <td>{{ user.description }}</td>
          <td>
            <button @click="openEditModal(user)" class="btn edit-btn">✏️</button>
            <button @click="deleteItem(user.id)" class="btn delete-btn">🗑️</button>
          </td>
        </tr>
      </tbody>
    </table>

    <button @click="showModal = true" class="btn add-btn">➕ เพิ่มผู้ใช้</button>

    <!-- Add/Edit User Modal -->
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <h2>{{ isEditing ? "แก้ไขผู้ใช้" : "เพิ่มผู้ใช้" }}</h2>
        <input type="text" v-model="form.firstname" placeholder="ชื่อ" required />
        <input type="text" v-model="form.lastname" placeholder="นามสกุล" required />
        <input type="number" v-model="form.age" placeholder="อายุ" required />
        <input type="text" v-model="form.gender" placeholder="เพศ" required />
        <input type="text" v-model="form.interests" placeholder="ความสนใจ" required />
        <input type="text" v-model="form.description" placeholder="คำอธิบาย" />

        <div class="modal-actions">
          <button @click="isEditing ? editItem() : addItem()" class="btn save-btn">
            {{ isEditing ? "บันทึกการเปลี่ยนแปลง" : "เพิ่มผู้ใช้" }}
          </button>
          <button @click="closeModal" class="btn cancel-btn">❌ ยกเลิก</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Navigation Bar Styles */
.navbar {
  background-color: #2c3e50; /* สีพื้นหลัง Navigation Bar */
  padding: 10px 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-radius: 10px;
  margin-bottom: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.navbar-brand {
  font-size: 24px;
  font-weight: 600;
  color: white;
}

.navbar-links {
  display: flex;
  gap: 20px;
}

.navbar-link {
  color: white;
  text-decoration: none;
  font-size: 16px;
  transition: color 0.3s ease;
}

.navbar-link:hover {
  color: #3498db; /* สีเมื่อโฮเวอร์ */
}

/* Header Styles */
.header {
  background-color: #3498db; /* สีพื้นหลัง Header */
  padding: 2px;
  border-radius: 10px;
  margin-bottom: 20px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  color: white; /* สีข้อความใน Header */
  text-align: center;
}

.header .title {
  font-size: 36px; /* ขนาดฟอนต์ใหญ่ขึ้น */
  margin-bottom: 10px;
  font-weight: 700; /* ตัวหนา */
  color: white;
}

.header .subtitle {
  font-size: 18px;
  color: #ecf0f1; /* สีข้อความรอง */
  font-weight: 400;
}

/* Container Styles */
.container {
  max-width: 1200px;
  margin: auto;
  padding: 20px;
  text-align: center;
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

/* Button Styles */
.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 14px;
  margin: 5px;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.btn:hover {
  transform: scale(1.05);
}

.add-btn {
  background-color: #3498db;
  color: white;
  margin-top: 20px;
}

.add-btn:hover {
  background-color: #2980b9;
}

.edit-btn {
  background-color: #f1c40f;
  color: white;
}

.edit-btn:hover {
  background-color: #f39c12;
}

.delete-btn {
  background-color: #e74c3c;
  color: white;
}

.delete-btn:hover {
  background-color: #c0392b;
}

/* Table Styles */
.user-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  background-color: white;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.user-table th,
.user-table td {
  border: 1px solid #ecf0f1;
  padding: 12px;
  text-align: center;
}

.user-table th {
  background-color: #3498db;
  color: white;
  font-weight: 600;
}

.user-table tr:nth-child(even) {
  background-color: #f9f9f9;
}

.user-table tr:hover {
  background-color: #ecf0f1;
  transition: background-color 0.3s ease;
}

/* Modal Styles */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  padding: 30px;
  border-radius: 10px;
  width: 400px;
  text-align: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

.modal-content h2 {
  margin-bottom: 20px;
  font-size: 24px;
  color: #2c3e50;
  font-weight: 600;
}

.modal input {
  width: 90%;
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #bdc3c7;
  border-radius: 5px;
  font-size: 14px;
}

.modal input:focus {
  border-color: #3498db;
  outline: none;
}

.modal-actions {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-top: 20px;
}

.save-btn {
  background-color: #2ecc71;
  color: white;
}

.save-btn:hover {
  background-color: #27ae60;
}

.cancel-btn {
  background-color: #e74c3c;
  color: white;
}

.cancel-btn:hover {
  background-color: #c0392b;
}
</style>