<script setup>
import { computed, ref, watch } from "vue";

let id = 0;
const newTodo = ref(null);
const todoData = ref([])
const todoId = ref(1)
const hideCompleted = ref(false);
const todos = ref([
  { id: id++, Text: "hello", done: true },
  { id: id++, Text: "hello world", done: false },
]);

const fetchData = async () => {
  try {
    todoData.value = null
    const res = await fetch(
      `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
    )
    todoData.value = await res.json()
  } catch (error) {
    console.error(error)
  }
}
fetchData()
watch(todoId, fetchData)
console.log(todoData)
const filteredTodos = computed(() => {
  return hideCompleted.value ? todos.value.filter((t) => !t.done) : todos.value;
});

function addTodo() {
  todos.value.push({
    id: id++,
    Text: newTodo.value,
    done: false,
  });
  newTodo.value = "";
}
function removeTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo);
}
</script>

<template>
  <form class="form" @submit.prevent="addTodo">
    <input v-model="newTodo" placeholder="Add New Task..." />
    <button :disabled="newTodo ? false : true">Add</button>
  </form>
  <div class="btn-block">
    <button @click="hideCompleted = !hideCompleted">
      {{ hideCompleted? "Show All": "HideCompleted" }}
    </button>
    <button @click="todoId++">Random Todos</button>
  </div>

  <ul class="task">
    <li v-for="todo in filteredTodos" :key="todo.id">
      <input type="checkbox" v-model="todo.done" />
      <span :class="{ done: todo.done }">{{ todo.Text }}</span>
      <button @click="removeTodo(todo)">X</button>
    </li>
    <li>
      Random Todo: <br>
      <span v-if="!todoData">Loading...</span>
      <span v-else><strong>" {{ todoData.title }} "</strong></span>
    </li>
  </ul>
</template>

<style>
.btn-block {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.form {
  display: flex;
  gap: 10px;
  width: inherit;
  margin-bottom: 2rem;
}

.form > input {
  width: inherit;
}

.task {
  margin-top: 2rem;
  padding: 0;
}

.task > li {
  list-style: none;
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 2rem;
}

.done {
  text-decoration: line-through;
}
</style>
