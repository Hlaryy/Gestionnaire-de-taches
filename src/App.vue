<script setup lang="ts">
import { ref, watch } from 'vue'

// Les valeurs autorisées pour le statut d'une tâche
type TaskStatus = 'to do' | 'in progress' | 'done'

// La structure d'une tâche
interface Task {
  id: number
  title: string
  status: TaskStatus
}

// Charge les tâches déjà enregistrées dans le navigateur
const savedTasks = localStorage.getItem('tasks')

// Liste réactive des tâches
const tasks = ref<Task[]>(
  savedTasks ? JSON.parse(savedTasks) as Task[] : []
)

// Contenu actuel du champ de saisie
const newTaskTitle = ref('')

// Message d'erreur à afficher s'il y a un problème
const error = ref('')

// Ajoute une tâche à la liste
function addTask(): void {
  const title = newTaskTitle.value.trim()

  if (!title) {
    error.value = 'Le titre de la tâche est obligatoire.'
    return
  }

  tasks.value.push({
    id: Date.now(),
    title: title,
    status: 'to do',
  })

  newTaskTitle.value = ''
  error.value = ''
}

// Affiche les tâches dans la console du navigateur
function affichTasks(): void {
  tasks.value.forEach((task) => {
    console.log(task.id)
    console.log(task.title)
    console.log(task.status)
  })
}

// Change le statut d'une tâche
function updateTaskStatus(id: number, status: TaskStatus): void {
  const task = tasks.value.find((item) => item.id === id)

  if (task) {
    task.status = status
  }
}

// Supprime une tâche de la liste
function deleteTask(id: number): void {
  tasks.value = tasks.value.filter((task) => task.id !== id)
}

// Enregistre la liste dans le navigateur chaque fois qu'elle change
watch(
  tasks,
  (newTasks) => {
    localStorage.setItem('tasks', JSON.stringify(newTasks))
  },
  { deep: true }
)
</script>

<template>
  <div class="todo-app">
    <!-- Header -->
    <header>
      <h1>Gestionnaire de Tâches</h1>
    </header>

    <!-- Formulaire d'ajout -->
    <main>
      <form @submit.prevent="addTask">
        <input 
          type="text" 
          v-model="newTaskTitle" 
          placeholder="Ajouter une nouvelle tâche..." 
        />
        <button type="submit">Ajouter</button>
      </form>

      <!-- Liste des tâches -->
      <ul class="task-list">
        <li v-for="task in tasks" :key="task.id" class="task-item">
          <span>{{ task.title }}</span>
          <select
            :value="task.status"
            @change="updateTaskStatus(task.id, ($event.target as HTMLSelectElement).value as TaskStatus)"
          >
            <option value="to do">to do</option>
            <option value="in progress">in progress</option>
            <option value="done">done</option>
          </select>
          <button @click="deleteTask(task.id)">Supprimer</button>
        </li>
      </ul>
    </main>

    <!-- Footer -->
    <footer>
      <p>To-Do Manager - Vue 3 & TypeScript</p>
    </footer>
  </div>
</template>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 40px auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

form {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

input {
  flex: 1;
  padding: 8px;
  font-size: 16px;
}

button {
  padding: 8px 16px;
  cursor: pointer;
}

.task-list {
  list-style: none;
  padding: 0;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ccc;
  gap: 10px;
}

.status {
  font-size: 12px;
  background: #eee;
  padding: 4px 8px;
  border-radius: 4px;
}
</style>
