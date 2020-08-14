<template>
  <div>
    <p v-on:mousemove="updateCoordinates">Coordinates {{ x }} / {{ y }}</p>
    <input
      v-model="newTodo"
      placeholder="Add a task"
      type="text"
      @change="handleChange"
      @keyup.enter="addTodo"
    />
    <button @click="addTodo">Add todo</button>
    <button @click="resetTodos">Reset</button>
    <div>
      <ul>
        <li
          :key="index"
          @click="todos[index].done = !done"
          class="lacami"
          :class="{done: todos[index].done}"
          v-for="(todo,index) in todos"
        >{{ todo.name }}</li>
      </ul>
    </div>
  </div>
</template>


<script>
import "../../styles/_base.css";
export default {
  name: "Input",
  props: {},
  data: function () {
    return {
      header: "Todo Input:",
      newTodo: "",
      todos: [],
      done: false,
      x: 0,
      y: 0,
    };
  },
  methods: {
    handleChange: function (e) {
      this.newTodo = e.target.value;
    },
    addTodo: function () {
      const newTodo = {
        name: this.newTodo,
        done: false,
      };
      this.todos.push(newTodo);
      this.newTodo = "";
    },
    deleteTodo: function () {},
    resetTodos: function () {
      return (this.todos = []);
    },
    updateCoordinates: function (e) {
      this.x = e.clientX;
      this.y = e.clientY;
    },
  },
  computed: {},
};
</script>