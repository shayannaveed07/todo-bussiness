<script setup>
import { ref, onMounted, computed, watch } from "vue";
let todos = ref([]);
let name = ref("");
let input_content = ref("");
let input_category = ref(null);
let todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

let addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });
  input_content.value = "";
  input_category.value = "";
};

let removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};
watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);
onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up <input type="text" placeholder="Name Here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a todo:</h3>
      <form @submit.prevent="addTodo">
        <h4>Whats on your Todo-list</h4>
        <input
          type="text"
          placeholder="e.g Buy a new car"
          v-model="input_content"
        />
        <h4>Pick a Category</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="Bussiness"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Bussiness</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="Personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>Todo list:</h3>
      <div class="list">
        <div
          v-for="(todo, index) in todos_asc"
          :class="`todo-item ${todo.done && 'done'}`"
          :key="index"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"> </span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
