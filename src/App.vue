<template>
  <div>
    <nav class="navbar bg-body-tertiary">
      <div class="container">
        <a class="navbar-brand" href="#">Aplikasi To Do List By Ignatius Loyola Farel Kusuma Dewa - V3422030</a>
      </div>
    </nav>

    <main class="container">
      <form class="mt-4" v-on:submit.prevent="saveTodo">
        <div class="mb-3">
          <label for="input-todo" class="form-label">Masukkan Aktivitas</label>
          <input
            type="text"
            class="form-control"
            id="input-todo"
            v-model="inputTodo"
          />
        </div>
        <div class="mb-3 form-check">
          <input
            type="checkbox"
            class="form-check-input"
            id="reminder"
            v-model="reminderTodo"
          />
          <label class="form-check-label" for="reminder">Ingatkan Saya</label>
        </div>
        <button type="submit" class="btn btn-primary">Simpan</button>
      </form>

      <section class="mt-5">
        <h4>ToDo List</h4>
        <ol class="list-group list-group-numbered">
          <li v-for="todo in paginatedTodoList" :key="todo.id"
            class="list-group-item d-flex justify-content-between align-items-start"
          >
            <div class="ms-2 me-auto">
              <div :class="{ 'fw-bold': todo.reminder }">{{ todo.text }}</div>
              {{ new Date(todo.createdAt).toLocaleTimeString() }}
            </div>
            <span class="badge text-danger rounded-pill fs-5" role="button" v-on:click="deleteTodo(todo.id)">
              <i class="bi bi-x-lg"></i>
            </span>
          </li>
        </ol>

        <nav aria-label="Page navigation" class="mt-4">
          <ul class="pagination">
            <li class="page-item" :class="{ 'disabled': currentPage === 1 }">
              <a class="page-link" href="#" @click="goToPage(currentPage - 1)" aria-label="Previous">
                <span aria-hidden="true">&laquo;</span>
              </a>
            </li>
            <li v-for="pageNumber in totalPages" :key="pageNumber" class="page-item" :class="{ 'active': pageNumber === currentPage }">
              <a class="page-link" href="#" @click="goToPage(pageNumber)">{{ pageNumber }}</a>
            </li>
            <li class="page-item" :class="{ 'disabled': currentPage === totalPages }">
              <a class="page-link" href="#" @click="goToPage(currentPage + 1)" aria-label="Next">
                <span aria-hidden="true">&raquo;</span>
              </a>
            </li>
          </ul>
        </nav>
      </section>
    </main>
  </div>
</template>

<script>
import { v4 as uuidV4 } from "uuid";

export default {
  name: "App",
  data() {
    return {
      inputTodo: "",
      reminderTodo: false,
      todoList: [],
      currentPage: 1,
      itemsPerPage: 15,
    };
  },
  computed: {
  paginatedTodoList() {
    const startIndex = (this.currentPage - 1) * this.itemsPerPage;
    const endIndex = startIndex + this.itemsPerPage;
    const slicedTodoList = this.todoList.slice(startIndex, endIndex);
    const startNumber = (this.currentPage - 1) * this.itemsPerPage + 1;
    const paginatedTodoList = slicedTodoList.map((todo, index) => ({
      ...todo,
      displayNumber: startNumber + index,
    }));

    return paginatedTodoList;
  },
  totalPages() {
    return Math.ceil(this.todoList.length / this.itemsPerPage);
  },
},
  methods: {
    saveTodo() {
      const newTodo = {
        id: uuidV4(),
        text: this.inputTodo,
        reminder: this.reminderTodo,
        createdAt: new Date(),
      };
      this.todoList = [newTodo, ...this.todoList];
      localStorage.setItem('todo', JSON.stringify(this.todoList))

      this.inputTodo = "";
      this.reminderTodo = false;
    },

    deleteTodo(id) {
      const newTodos = this.todoList.filter((todo) => todo.id !== id);

      this.todoList = newTodos; 
      localStorage.setItem('todo', JSON.stringify(this.todoList))
    },

    goToPage(pageNumber) {
      if (pageNumber >= 1 && pageNumber <= this.totalPages) {
        this.currentPage = pageNumber;
      }
    },
  },
  mounted() {
    this.todoList = JSON.parse(localStorage.todo || "[]");
  }
};
</script>