<template>
  <v-app>
    <v-main>
      <v-container class="fill-height">
        <v-row justify="center">
          <v-col cols="12" md="3">
            <v-card>
              <v-card-text>
                <!-- new -->
                <v-btn class="mb-3" block color="error" @click="logout" v-if="isAuthenticated">Logout</v-btn>
                <!-- new -->
                <h1 class="title">{{ message }}</h1>
                <v-form @submit.prevent="login" v-if="!isAuthenticated">
                  <v-text-field v-model="credentials.username" variant="outlined" label="Username"></v-text-field>
                  <v-text-field v-model="credentials.password" variant="outlined" label="Password"></v-text-field>
                  <v-btn type="submit" block color="primary">Login</v-btn>
                </v-form>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup>
import { reactive, ref } from 'vue';

const message = ref("")
const isAuthenticated = ref(false)
const credentials = reactive({ username: "", password: "" })

const login = () => {
  fetch("/api/login/",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(credentials)
    }
  ).then(response=>{
    if(response.ok){
      isAuthenticated.value=true
      sayHello()
    }
    return response.json()
  })
  .then(data=>console.log(data)
  )
}
const sayHello = () => {
  fetch("/api/hello/",
    {
      method: "GET",
      headers: {
        "Content-Type": "application/json",
      },
    }
  ).then(response=>response.json())
  .then(data=>{
    message.value = data.message
  }
  )
}
//new
const logout = () => {
  fetch("/api/logout/",
    {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "X-CSRFToken":getCsrfToken() // new
      }
    }
  ).then(response=>{
    if(response.ok){
      isAuthenticated.value=false
    }
    return response.json()
  })
  .then(data=>console.log(data)
  )
}

const getCsrfToken = () => {
  const cookie = document.cookie.split(';')[0];
  const value = cookie.split("=")[1]
  return value
}

</script>
