<template>
    <div>
      <h1>Todo List</h1>
  
      <div>
        <h2>Create Todo</h2>
        <form @submit.prevent="createTodo">
          <input v-model="newTodo.name" placeholder="Todo name" required />
          <input v-model="newTodo.description" placeholder="Todo description" />
          <button type="submit">Add</button>
        </form>
      </div>
  
      <div>
        <h2>Todos</h2>
        <ul>
          <li v-for="todo in todos" :key="todo.id">
            {{ todo.name }} - {{ todo.is_completed ? 'Complete' : 'Incomplete' }}
            <button @click="markAsComplete(todo.id)" :disabled="todo.is_completed">Mark as Complete</button>
            <button @click="editTodo(todo)">Edit</button>
            <button @click="deleteTodo(todo.id)">Delete</button>
          </li>
        </ul>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        todos: [],
        newTodo: {
          name: '',
          description: ''
        },
        editTodoData: null
      };
    },
    mounted() {
      this.fetchTodos();
    },
    methods: {
      fetchTodos() {
        axios.get('/api/todos').then(response => {
          this.todos = response.data;
        });
      },
      createTodo() {
        axios.post('/api/todos', this.newTodo).then(response => {
          this.todos.push(response.data);
          this.newTodo = { name: '', description: '' };
        });
      },
      markAsComplete(id) {
        axios.patch(`/api/todos/${id}/complete`).then(() => {
          this.fetchTodos();
        });
      },
      editTodo(todo) {
        this.newTodo = todo;
      },
      deleteTodo(id) {
        axios.delete(`/api/todos/${id}`).then(() => {
          this.fetchTodos();
        });
      }
    }
  };
  </script>
  