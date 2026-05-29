<script setup lang="ts">
import { ref } from 'vue'
import Login from './Components/Login.vue'
import Dashboard from './Components/Dashboard.vue'

const isLoggedIn = ref(false)
const loggedInUser = ref('')

const handleLoginSuccess = (payload: { user: string; role: string }) => {
  loggedInUser.value = payload.user
  isLoggedIn.value = true
}

const handleLogout = () => {
  isLoggedIn.value = false
  loggedInUser.value = ''
}
</script>

<template>
  <Login v-if="!isLoggedIn" @login-success="handleLoginSuccess" />
  <Dashboard v-else :username="loggedInUser" @logout="handleLogout" />
</template>

<style>
#app {
  max-width: 100% !important;
  margin: 0 !important;
  padding: 0 !important;
  text-align: left !important;
  width: 100vw;
  height: 100vh;
}

* {
  box-sizing: border-box;
}

body, html { 
  margin: 0; 
  padding: 0; 
  background: #000000;
  font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
</style>
