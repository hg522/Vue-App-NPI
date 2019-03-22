<template>
  <div id="app">
    <router-view @validUser="setValidUser"></router-view>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      validUser:false
    }
  },
  mounted() {
    if(!this.validUser) {
      this.$router.replace({ name: "login" });
    }
  },
  methods:{
      setValidUser(valid){
          this.validUser = valid;
      }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height:100%;
  width:100%;
  /*margin-top: 60px;*/
}
</style>
