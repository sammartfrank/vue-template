<template>
  <div id="counter">
    <h1>{{ header }}</h1>
    <div>
      <h2>{{ counter }} | {{ output }}</h2>
      <h2>Result: {{ result() }} $ EARNED</h2>
      <button @click="counter++">increase Counter</button>
      <button @click="counter--">Decrease Counter</button>
      <button @click="reset">reset</button>
    </div>
  </div>
</template>


<script>
export default {
  name: "Counter",
  data: function () {
    return {
      header: "Todo Counter",
      counter: 0,
    };
  },
  computed: {
    output: function () {
      return this.counter <= 0 ? "No Money" : this.counter;
    },
  },
  methods: {
    reset: function () {
      return (this.counter = 0);
    },
    result: function () {
      return this.counter <= 0 ? 0 : this.counter;
    },
  },
  watch: {
    counter: function () {
      let vm = this;
      setTimeout(function () {
        vm.counter = 0;
      }, 10000);
    },
  },
};
</script>


<style lang="css" scoped>
#counter {
  background: cornsilk;
}
</style>