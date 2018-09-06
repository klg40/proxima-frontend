<template>
  <div id="app" ref="app">
    <div class="buttons" v-if="!user && !control">
        <button @click="user = true">Пользователь</button>
        <button @click="control = true">Менеджер</button>
    </div>
    <User v-if="user"></User>
    <Control v-if="control"></Control>
    
  </div>
</template>

<script>
import User from './components/User.component'
import Control from './components/Control.component'

export default {
  name: 'app',
  components : { User, Control },
  data : function(){
    return {
      message : '',
      password : null,
      ws : null,
      clientPass: '',
      user : false,
      control : false
    }
  }
}
</script>

<style>
body {
  margin: 0
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: absolute;
  background-color: #d2d2d2;
  width: 100%;
  height: 300%;
}

.cursor {
  width: 15px;
  height: 15px;
  position: fixed;
  border-radius: 50%;
  background-color: green;
}
</style>
