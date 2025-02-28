<template>
  <div id="app">
    <UsersList />  <!-- เรียกใช้ component ที่สร้างขึ้น -->
  </div>
</template>

<script>
import UsersList from './components/UsersList.vue'

export default {
  name: 'App',
  components: {
    UsersList
  }
}
</script>
