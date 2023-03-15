<template>
  <div>
    <hr>

    <p>LessonCount: {{ count.lesson }}</p>
    <button @click="increment">Count Up!</button>

    <hr>

    <h1 :class="titleClass">Title: {{ title }}</h1>

    <hr>

    <input v-model="inputText">
    <p>{{ inputText }}</p>

    <hr>

    <button @click="toggle">Toggle</button>
    <h1 v-if="awesome">Vue is awesome!</h1>
    <h1 v-else>Oh no...</h1>

    <hr>

    <form @submit.prevent="addTodo">
      <input v-model="newTodo">
      <button>Add Todo</button>    
    </form>
    <ul>
      <li v-for="todo in filteredTodos" :key="todo.id">
        <input type="checkbox" v-model="todo.done">
        <span :class="{ done: todo.done }">{{ todo.text }}</span>
        <button @click="removeTodo(todo)">X</button>
      </li>
    </ul>
    <button @click="hideCompleted = !hideCompleted">
      {{ hideCompleted ? 'Show all' : 'Hide completed' }}
    </button>

    <hr>

    <p ref="p">hello</p>

    <hr>

    <p>Todo id: {{ todoId }}</p>
    <button @click="todoId++">Fetch next todo</button>
    <p v-if="!todoData">Loading...</p>
    <pre v-else>{{ todoData }}</pre>

    <hr>

    <ChildComp
      :msg="greeting"
      @response="(msg) => childMsg = msg"
    />

    <hr>

    <p>{{ childMsg }}</p>

    <hr>

    <ChildCompSlot>Message: {{ msg }}</ChildCompSlot>

    <hr>
  </div>
</template>

<script setup>
import { reactive, ref, computed, onMounted, watch } from 'vue'
import ChildComp from './components/ChildComp.vue'
import ChildCompSlot from './components/ChildCompSlot.vue'

const count = reactive({ lesson: 0 })
const title = ref('Hello Vue Tutorial!')
const titleClass = ref('title')
const inputText = ref('')
const awesome = ref(true)

let id = 0

const newTodo = ref('')
const hideCompleted = ref(false)
const todos = ref([
  { id: id++, text: 'Learn HTML', done: true },
  { id: id++, text: 'Learn JavaScript', done: true },
  { id: id++, text: 'Learn Vue', done: false }
])

const p = ref(null)

const todoId = ref(1)
const todoData = ref(null)

const greeting = ref('Hello from parent')
const childMsg = ref('No child msg yet')
const msg = ref('from parent')

function increment() {
  count.lesson++
}

function toggle() {
  awesome.value = !awesome.value
}

function addTodo() {
  todos.value.push({ id: id++, text: newTodo.value, done: false })
  newTodo.value = ''
}

function removeTodo(todo) {
  todos.value = todos.value.filter((t) => t !== todo)
}

const filteredTodos = computed(() => {
  return hideCompleted.value
    ? todos.value.filter((t) => !t.done)
    : todos.value
})

async function fetchData() {
  todoData.value = null
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  )
  todoData.value = await res.json()
}

fetchData()

watch(todoId, fetchData)

onMounted(() => {
  p.value.textContent = 'mounted!'
})
</script>

<style>
hr {
  margin: 10px 0px;
}

.title {
  color: red;
}

.done {
  text-decoration: line-through;
}
</style>