<script setup lang="ts">
/**
 * ref -> wie das State behandelt wird
 * onMounted -> was wird gezeigt von localstorage
 * computed -> effiziente Weise mit Änderung und Manipulation den Daten
 * watch -> beobachten, ob eine Änderung in Ref passiert und aktualisiert sie im localstorage
 */
import {ref, onMounted, computed, watch} from "vue"

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.created_at - a.created_at
}))

const addtodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    created_at: new Date().getTime(),
  })
  //entleeren das Eingabefeld nach der Eingabe
  input_content.value=''
  input_category.value=null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(name, (newVal) => {
  localStorage.setItem('name', newVal) // um der Name zu speichern //z.55
})
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})
onMounted(() => {
  name.value = localStorage.getItem('name') || '' // getter vom Name
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hallo, <input type="text" placeholder="Name hier" v-model="name"/>
      </h2>
    </section>

    <section class="create-todo">
      <h3>ToDo erstellen</h3>

      <form @submit.prevent="addtodo">

        <h4>Was steht auf Ihrer Todo-Liste?</h4>
        <input type="text" placeholder="Z.B. Ein Buch lesen" v-model="input_content"/>

        <h4>Eine Kategorie wählen</h4>
        <div class="options">

          <label>
            <input type="radio" name="category" value="study" v-model="input_category"/>
            <span class="bubble study"></span>
            <div>Studium</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Persönlich</div>
          </label>

        </div>
        <input type="submit" value="Todo hinzufügen"/>
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LISTE</h3>

      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">x</button>
          </div>

        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
header {
  line-height: 2;
  max-height: 100vh;
}

</style>