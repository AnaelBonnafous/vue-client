<script setup lang="ts">
import { onMounted, ref, toRaw } from "vue";
import axios from "axios";

axios.defaults.withCredentials = true;
axios.defaults.baseURL = "http://localhost:82";

const form = ref({
  email: "test@example.com",
  password: "password",
});

const user = ref({});

onMounted(async () => {
  await fetchUser();
});

const fetchUser = async () => {
  try {
    const { data } = await axios.get("/api/user");
    user.value = data;
  } catch (error) {
    // console.log(error);
  }
};

const login = async () => {
  try {
    await axios.get("/sanctum/csrf-cookie");
    await axios.post("/login", toRaw(form.value));
    await fetchUser();
  } catch (error) {
    // console.log(error);
  }
};

const logout = async () => {
  await axios.post("/logout");
  user.value = {};
};
</script>

<template>
  <main>
    <img
      alt="Vue logo"
      class="logo"
      src="./assets/logo.svg"
      width="125"
      height="125"
    />

    <form @submit.prevent="login">
      <div>
        <input v-model="form.email" type="email" />
      </div>
      <div>
        <input v-model="form.password" type="password" />
      </div>
      <button type="submit">login</button>
    </form>

    <pre>
      {{ user }}
    </pre>

    <button @click="logout">logout</button>
  </main>
</template>

<style scoped>
main {
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  gap: 20px;
}

form {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>
