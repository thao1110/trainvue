<template>
  <div id="app">
    <section class="todoapp">
    <header class="header">
      <h1 v-text="title"></h1>
      <input class="new-todo" placeholder="What needs to be done?" v-on:keyup.enter="createTodo" autofocus/>
    </header>
    <ul class="todo-list">
      <li v-for="todo in todos":class="{ completed:todo.is_done, editing: todo === editing }" v-bind:key="todo.text">
        <div class="view">
          <input class="toggle" type="checkbox" v-model="todo.is_done"/>
          <label @dblclick="startEditing(todo)">{{ todo.title }}</label>
          <button class="destroy" @click="destroyTodo(todo)"></button>
        </div>
        <input class="edit" 
          @keyup.esc="cancelEditing"
          @keyup.enter="finishEditing"
          @blur="finishEditing"
          :value="todo.description"/>
      </li>
    </ul>
  </section>
  </div>
</template>

<script>
const axios = require('axios');
export default {
  name: 'App',
  data: function () {
    return {
      title: "Learn Js!",
      // todos: [
      //   { text:'Learn Javacript ES6+ goodies', isDone:true },
      //   { text:'Learn Vue', isDone:false },
      //   { text: 'Build something awesome', isDone:false }
      // ],
      todos:null,
      editing: null,
    }
  },
  mounted () {
    this.loadAsync();
  },
  methods: {
    createAsync(title, description) {
      var self = this;
      // call api to create new job here
      axios.post('https://602d2ff930ba720017223fd7.mockapi.io/api/v1/jobs', {
        title: title,
        description: description,
        is_done: false
      })
      .then((response) => {
         self.todos.push(response.data);
      }, (error) => {
        console.log(error);
      });

    },
    loadAsync() {
      axios
        .get('https://602d2ff930ba720017223fd7.mockapi.io/api/v1/jobs')
        .then(response => (this.todos = response.data))
        .catch(error => alert(error));

    },
    createTodo(event) {
      var textbox = event.target;
      this.createAsync('new_title___', textbox.value);
      textbox.value = "";
    },
    startEditing(todo) {
      this.editing = todo;
    },
    finishEditing(event) {
      if(!this.editing) { return; }
      const textbox = event.target;
      this.editing.text = textbox.value;
      this.editing = null;
    }, 
    cancelEditing() {
      this.editing = null;
    },
    destroyTodo(todo) {
      const index = this.todos.indexOf(todo);
      this.todos.splice(index,1);
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
  margin-top: 60px;
}
</style>
