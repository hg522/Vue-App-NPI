<template>
  <div class="container main-container d-flex align-items-center">
    <div class="container form-container rounded-lg d-flex align-items-center">
      <form class="form-signin">
        <!--<img src = "../assets/user2.png" class="rounded-circle" height="100px" width="100px" alt="">-->
        <img src = "../assets/North-Park-logo.png" class="rounded-circle" height="100px" width="100px" alt="">
        <div class="pb-4"></div>
        <h3 class="h4 pb-3 heading-text font-weight-normal">Member Login</h3>
        <label for="username" class="sr-only">Username</label>
        <input type="text" id="username" class="form-control text-white username" v-model="username" autocomplete="off" placeholder="Username" required>
        <div class=""></div>
        <label for="password" class="sr-only">Password</label>
        <input type="password" id="password" class="rounded form-control text-white pass" v-model="password" placeholder="Password" required>
        <div v-if="loginError" class="alert alert-danger" role="alert">
          Incorrect Username or Password!
        </div>
        <div class="pb-3 pt-2"></div>
        <button class="btn btn-lg btn-dark btn-block rounded log" v-on:click.stop.prevent="login()" type="submit">Login</button>
        <p class="mt-4 mb-3 text-muted">© North Park Innovations</p>
      </form>
    </div>
  </div>
</template>

<script>
  import json from '../assets/json/User.json'
  export default {
    name: 'login',
    data() {
      return {
        username: "",
        password: "",
        userData:json,
        loginError:false
      }
    },
    watch:{
      username(){
          this.loginError=false;
      },
      password(){
          this.loginError=false;
      }
    },
    methods: {
      login() {
        var validUser = false;
        if(this.username != "" && this.password != "") {
          for(var i = 0; i < this.userData.length; i++) {
            var user = json[i];
            if(user.id == this.username && user.password == this.password){
              validUser = true;
              break;
            }
          }
          if(validUser){
            this.loginError=false;
            this.$emit("validUser", true);
            this.$router.replace({ name: "home" });
          }else{
            this.loginError=true;
          }
        }
      }
    }
  }
</script>

<style scoped>
  .main-container{
    height: 100%;
    width: 100%;
    max-width: 100%;
    /*background: linear-gradient(to right top, #4d18a5, #462dae, #3e3db7, #374abe, #3257c4, #3365cb, #3972d2, #417fd7, #5392df, #68a4e6, #7fb6ec, #98c8f2);
  */
    /*background-image: linear-gradient(to right top, #431393, #451f98, #48289e, #4a31a3, #4d39a8, #4d4ab3, #4e59bd, #5168c6, #5881d4, #6799e0, #7db1e9, #98c8f2);*/
    /*background-image: linear-gradient(to right top, #051937, #234166, #406e98, #5f9fcb, #7fd3ff);*/
    /*background: linear-gradient(-135deg,#c850c0,#4158d0);*/
    /*background-image: linear-gradient(to right top, #111315, #181a1c, #1e2124, #25282b, #2c2f33);*/
    background-image: linear-gradient(to right top, #111315, #141517, #171819, #1a1a1a, #1c1c1c);
    /*background: lightgrey;*/
  }

  .form-container{
    /*background: #fff;*/
    height: 95%;
    width: 450px;
    max-width: 100%;
    /*background: #fff;*/
    background: transparent;
  }

  .form-signin {
    width: 100%;
    width: 330px;
    padding: 15px;
    margin: 0 auto;
  }

  .form-signin .form-control {
    position: relative;
    box-sizing: border-box;
    height: auto;
    padding: 10px;
    font-size: 16px;
  }
  .form-signin .form-control:focus {
    z-index: 2;
  }
  .form-signin input[type="email"] {
    margin-bottom: -1px;
    border-bottom-right-radius: 0;
    border-bottom-left-radius: 0;
  }
  .form-signin input[type="password"] {
    margin-bottom: 10px;
    border-top-left-radius: 0;
    border-top-right-radius: 0;
  }

  .btn-dark:hover{
    color: #fff;
    /*background-color: #57b846;
    border-color: #57b846;*/
    /*background-color: #5488dd;*/
    background-color: #5488dd;
    border-color: #5488dd;
  }
  .username{
    background-color: #2c2f33;
    border-color: #2c2f33;
  }
  .pass{
    margin-top:4px;
    background-color: #2c2f33;
    border-color: #2c2f33;
  }
  .log{
    background-color: transparent;
    border-color: #2c2f33;
    color: #b3b9bf;
    font-size:18px;
  }
  .heading-text{
    color:#b3b9bf;
  }

  input[type=text]{
    background-color: #2c2f33;
  }

  input[type=text]:focus {
    background-color: #2c2f33;
  }

  input[type=text]:-webkit-autofill {
    -webkit-text-fill-color: #2c2f33 !important;
  }
  input[type=text]::selection{
    background-color: #2c2f33;
  }

  .alert-danger {
    color: #992230;
    background-color: rgba(248, 180, 180, 0.78);
    border-color: transparent;
    padding:8px;
  }
</style>