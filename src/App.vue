<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" class="logo" />
    <ApolloExample />
  </div>
</template>

<script>
import ApolloExample from './components/ApolloExample.vue';

export default {
  name: 'App',
  components: {
    ApolloExample,
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.logo {
  position: absolute;
  top: 10px;
  left: 10px;
  width: 150px;
}
</style>
