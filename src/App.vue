<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

// Trier les tâches dans l'ordre ascendant
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

// Ajouter une tâche
const addTodo = () => {
  if (input_content.value.trim() === '' | input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

// Supprimer une tâche
const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

// Stocker dans le localStorage les nouvelles valeurs
watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue));
}, { deep: true })

watch(name, (newValue) => {
  localStorage.setItem('name', newValue)
})

// Récupérer les informations
onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Bonjour, <input type="text" placeholder="votre prénom" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">

      <form @submit.prevent="addTodo">
        <h4>Créer une tâche</h4>
        <input type="text" placeholder="ex: Apprendre VueJS" v-model="input_content" />
        <h4>Choisissez une catégorie</h4>

        <div class="options">
          <label for="category1">
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Professionel</div>
          </label>

          <label for="category2">
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personel</div>
          </label>
        </div>

        <input type="submit" value="Ajouter la tâche">
      </form>
    </section>

    <section class="todo-list">
      <h3>Liste des tâches</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category == 'business' ? 'business' : 'personal'}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Supprimer</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

