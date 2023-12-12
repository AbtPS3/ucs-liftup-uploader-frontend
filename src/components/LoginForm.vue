
<template>
  <div class="form-group">
    <form @submit.prevent="login">
      <div class="input-group input-mid">
        <label for="username">Email</label>
        <input v-model="username" type="text" class="input-text" id="username" name="username" autocomplete="username">
      </div>
      <div class="input-group input-mid">
        <label for="password">Password</label>
        <input v-model="password" type="password" class="input-text" id="password" name="password">
      </div>
      <div class="input-group input-mid">
        <input type="submit" value="Login" class="input-btn">
      </div>
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

import { useRouter } from 'vue-router';
import { useToken } from '../stores/store';
import { useAlert } from '../stores/store';

const username = ref('');
const password = ref('');
const router = useRouter();

const login = async () => {
  // Check if username or password is empty
  if (username.value === '' || password.value === '') {
    useAlert.toggleVisibility(true);
    useAlert.changeType('alert-warning');
    useAlert.changeTitle('WARNING');
    useAlert.changeMessage('Username and password are required!');
    setTimeout(() => {
      useAlert.toggleVisibility(false);
    }, 5000);
    return; // Return early if validation fails
  }
  try {
    const backendUrl = import.meta.env.VITE_BACKEND_URL;
    const backendPort = import.meta.env.VITE_BACKEND_PORT;
    const backendAddress = backendUrl + ":" + backendPort + "/api/v1/uploads/login";
    const response = await axios.post(backendAddress, {
      username: username.value,
      password: password.value
    });

    // Store the token in Vue store
    const token = response.data.payload.token;
    useToken.setToken(token);

    // Redirect the user to /uploads route
    await router.push('/upload');
  } catch (error) {
    useAlert.toggleVisibility(true);
    useAlert.changeType("alert-error");
    useAlert.changeTitle("FAILED");
    useAlert.changeMessage("Wrong username or password!");
    setTimeout(() => {
      useAlert.toggleVisibility(false);
    }, 5000);
  }
};
</script>

<style scoped>
.form-group {
  position: relative;
  display: flex;
  height: 100%;
  width: 100%;
  flex-direction: column;
  justify-content: space-evenly;
  padding-left: 20%;
  overflow: hidden;
  ;
}

.input-mid {
  position: relative;
  display: flex;
  flex-direction: column;
  width: 60%;
  margin-top: 5%;
}

.input-group-tall {
  position: relative;
  display: flex;
  width: 80%;
  margin-top: 5%;
}

.input-group label {
  font-size: 0.9em;
  color: #999;
  padding: 0 0 5px 10px;
}

.input-group input {
  height: 50px;
  border-radius: 5px;
  border: 1px solid #c3c3c3;
  font-size: 1.2em;
}

.input-group input:focus {
  border: 1px solid #989898;
  outline: none !important;
  color: #333;
  font-size: 1.2em;
}

.input-btn {
  position: relative;
  margin-top: 10%;
  cursor: pointer;
  height: 40px;
  border-radius: 5px;
  border: 1px solid #c3c3c3;
  color: #666;
  font-size: 1.2em;
}

.input-btn:hover {
  background-color: #fff;
}
</style>