<template>
  <div class="login">
    {{isLoggedIn()}}
    <form v-on:submit.prevent="submit()">
      <h1>Login</h1>
      <ul>
        <li class="text-danger" v-for="error in errors">{{ error }}</li>
      </ul>
      <div class="form-group">
        <label>username:</label>
        <input type="text" class="form-control" v-model="username">
      </div>
      <div class="form-group">
        <label>Password:</label>
        <input type="password" class="form-control" v-model="password">
      </div>
      <input type="submit" class="btn btn-primary" value="Submit">
      <router-link id ="signOut"class="btn btn-round" to="/logout" tag="button">{{signOut()}}</router-link>    
      </form>
  </div>
</template>
<style>
button#signOut {
  margin-left: 10px;
  height: 35px;
  align-content: center;
}
</style>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      loginStatus: "",
      username: "",
      password: "",
      errors: [],
    };
  },
  methods: {
    signOut: function () {
      if (this.loginStatus === "true") {
        return "Sign Out";
      } else {
        return "";
      }
    },
    submit: function () {
      var params = {
        username: this.username,
        password: this.password,
      };
      axios
        .post("api/sessions", params)
        .then((response) => {
          axios.defaults.headers.common["Authorization"] =
            "Bearer " + response.data.jwt;
          localStorage.setItem("jwt", response.data.jwt);
          localStorage.setItem("Username", response.data.username);
          this.$router.push("/dashboard");
        })
        .catch((error) => {
          this.errors = ["Invalid username or password."];
          this.username = "";
          this.password = "";
        });
    },
    isLoggedIn: function () {
      if (localStorage.getItem("jwt")) {
        this.loginStatus = "true";
      } else {
        this.loginStatus = "false";
      }
    },
  },
};
</script>