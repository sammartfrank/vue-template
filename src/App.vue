<template>
  <div id="app">
    <img alt="Vue logo" height="150" src="./assets/logo.gif" />
    <HelloWorld msg="Todo App in Vue" />
    <Input />
    <Counter />
  </div>
</template>


<script>
import HelloWorld from "./components/HelloWorld.vue";
import Input from "./components/Input/Input";
import Counter from "./components/List/Counter";

export default {
  name: "App",
  components: {
    HelloWorld,
    Input,
    Counter,
  },
};
</script>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #1a70c5;
  margin-top: 60px;
}
</style>
