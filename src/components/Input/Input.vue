<template>
  <div class="todosection" v-on:mousemove="updateCoordinates">
    <div class="progress">
      <div :style="myStyle" />
    </div>
    <p>Coordinates {{ x }} / {{ y }}</p>
    <input
      v-model="newTodo"
      placeholder="Add a task"
      type="text"
      @change="handleChange"
      @keyup="handleError"
      @keyup.enter="addTodo"
    />
    <button class="vuButton" @click="addTodo">+</button>
    <button class="rsetbutton" @click="resetTodos">Reset</button>
    <div class="error" v-if="error">{{ error }}</div>
    <div>
      <ul :style="{margin: '0', padding: '0'}">
        <li
          :key="index"
          @click="todos[index].done = !done"
          class="lacami"
          :class="{completed: todos[index].done, lacami: !todos[index].done }"
          v-for="(todo,index) in todos"
        >
          {{ todo.name }}
          <span v-if="todos[index].done">(done)</span>
        </li>
      </ul>
      <div>
        <p>{{ calcCompleted }} / {{ calcTodos }}</p>
      </div>
      <div
        class="dynamo"
        :style="{
          width: width + 'px',
          height: width + 'px',
          backgroundColor: color,
          margin: '0 auto',
          marginBottom: '20px'
          }"
      />
      <!-- <div
        class="dynamo"
        :style="[myStyle, { width: width + 'px', backgroundColor: color }]"
      ></div>-->
      <input type="text" placeholder="config color" v-model="color" />
      <input type="text" placeholder="config percentage width" v-model="width" />
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
      width: 100,
      height: 100,
      todos: [],
      color: "",
      proColor: "",
      done: false,
      x: 0,
      y: 0,
      error: null,
    };
  },
  methods: {
    handleChange: function (e) {
      this.newTodo = e.target.value;
    },
    handleError: function () {
      this.error = null;
    },
    addTodo: function () {
      const newTodo = {
        name: this.newTodo,
        done: false,
      };
      if (!this.newTodo.length) {
        this.error = new Error("You need to write a To-Do");
        return;
      }
      this.todos.push(newTodo);
      this.newTodo = "";
    },
    deleteTodo: function () {},
    resetTodos: function () {
      this.error = null;
      this.todos = [];
    },
    updateCoordinates: function (e) {
      this.x = e.clientX;
      this.y = e.clientY;
    },
  },
  computed: {
    calcTodos: function () {
      return this.todos.length;
    },
    calcCompleted: function () {
      let doneNumb = 0;
      this.todos.map((e) => {
        return e.done ? doneNumb++ : 0;
      });
      return doneNumb;
    },
    myStyle: function () {
      let pers =
        (parseInt(this.calcCompleted, 10) / parseInt(this.calcTodos, 10)) * 100;
      if (isNaN(pers)) {
        pers = 0;
      }
      return {
        backgroundColor:
          pers <= 25
            ? "red"
            : pers <= 60
            ? "orange"
            : pers <= 90
            ? "lime"
            : pers <= 100
            ? "green"
            : null,
        transition: "all 1s",
        height: "10px",
        width: `${pers}%`,
      };
    },
  },
};
</script>