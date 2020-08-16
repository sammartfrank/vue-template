<template>
  <div id="app">
    <img alt="Vue logo" width="100" src="./assets/logo.gif" />
    <Input />
    <Counter />
    <Game />
  </div>
</template>


<script>
import Input from "./components/Input/Input";
import Counter from "./components/List/Counter";
import Game from "./components/Game/Game";

export default {
  name: "App",
  components: {
    Input,
    Counter,
    Game,
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
  margin-top: 30px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>
