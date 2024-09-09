<template>
    <div class="container mt-4">
      <h1 class="mb-4">Todo List</h1>
  
      <div class="mb-4">
        <h2>Create Todo</h2>
        <form @submit.prevent="createTodo">
          <div class="mb-3">
            <label for="todoName" class="form-label">Todo Name</label>
            <input 
              id="todoName" 
              v-model="newTodo.name" 
              type="text" 
              class="form-control" 
              placeholder="Enter todo name" 
              required 
            />
          </div>
          <div class="mb-3">
            <label for="todoDescription" class="form-label">Description</label>
            <input 
              id="todoDescription" 
              v-model="newTodo.description" 
              type="text" 
              class="form-control" 
              placeholder="Enter todo description" 
            />
          </div>
          <button type="submit" class="btn btn-primary">Add</button>
        </form>
      </div>
  
      <div class="mb-4">
        <!-- Tabs for switching between All Todos and Completed Todos -->
        <ul class="nav nav-tabs">
          <li class="nav-item">
            <a 
              class="nav-link" 
              :class="{ active: currentTab === 'all' }" 
              href="#" 
              @click.prevent="currentTab = 'all'"
            >
              All Todos
            </a>
          </li>
          <li class="nav-item">
            <a 
              class="nav-link" 
              :class="{ active: currentTab === 'completed' }" 
              href="#" 
              @click.prevent="currentTab = 'completed'"
            >
              Completed Todos
            </a>
          </li>
        </ul>
        <div class="mt-3">
          <ul class="list-group">
            <li v-for="todo in filteredTodos" :key="todo.id" class="list-group-item d-flex justify-content-between align-items-center">
              {{ todo.name }} - {{ todo.description }} 
              <span :class="{'text-success': todo.is_completed, 'text-danger': !todo.is_completed}">
                {{ todo.is_completed ? 'Complete' : 'Incomplete' }}
              </span>
              <div>
                <button 
                  @click="markAsComplete(todo.id)" 
                  :disabled="todo.is_completed"
                  class="btn btn-success btn-sm me-2"
                >
                  Mark as Complete
                </button>
                <!-- Conditionally render the Edit button based on the is_completed status -->
                <button 
                  v-if="!todo.is_completed"
                  @click="editTodo(todo)"
                  class="btn btn-warning btn-sm me-2"
                >
                  Edit
                </button>
                <button 
                  @click="deleteTodo(todo.id)"
                  class="btn btn-danger btn-sm"
                >
                  Delete
                </button>
              </div>
            </li>
          </ul>
        </div>
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
        editTodoData: null,
        currentTab: 'all' // Default tab
      };
    },
    computed: {
      filteredTodos() {
        if (this.currentTab === 'completed') {
          return this.todos.filter(todo => todo.is_completed);
        }
        return this.todos;
      }
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
  
  <style scoped>
  .nav-tabs .nav-link.active {
    color: #495057;
    background-color: #e9ecef;
    border-color: #dee2e6 #dee2e6 #fff;
  }
  </style>
  