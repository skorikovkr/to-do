<script setup>
import { ref, provide, computed, watch } from 'vue'
import NewTaskForm from './components/NewTaskForm.vue'
import TaskItem from './components/TaskItem.vue'

const tasks = ref(JSON.parse(localStorage.getItem('tasks')) ?? [])
const searchString = ref(localStorage.getItem('searchString'))

provide('tasks', tasks)

watch(searchString, () => {
  localStorage.setItem('searchString', searchString.value)
})

const tasksUpdated = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}

const handleTaskAdded = (task) => {
  tasks.value.push(task)
  tasksUpdated()
}

const handleTaskDeleted = (task) => {
  const taskId = task.id
  const index = tasks.value.findIndex((t) => t.id === taskId)
  if (index > -1) {
    tasks.value.splice(index, 1)
  }
  tasks.value
    .filter((t) => t.parentId === taskId)
    .forEach((st) => {
      handleTaskDeleted(st)
    })
  tasksUpdated()
}

const handleTaskDone = (task) => {
  task.done = !task.done
  tasksUpdated()
}

const handleTitleChanged = (task, title) => {
  task.title = title
  tasksUpdated()
}

const filteredTasks = computed(() => {
  if (searchString.value) {
    return tasks.value.filter((t) =>
      t.title.toLowerCase().includes(searchString.value.toLowerCase())
    )
  }
  return tasks.value
})
</script>

<template>
  <main>
    <header>
      <div class="filters">
        <label for="filter">Search:</label>
        <input name="filter" v-model="searchString" />
      </div>
      <p>Add new task:</p>
      <NewTaskForm :parent-id="null" @task-added="handleTaskAdded" />
    </header>
    <TaskItem
      v-for="t in searchString ? filteredTasks : tasks.filter((t) => !t.parentId)"
      :task="t"
      :key="t.id"
      :includeSubTasks="!searchString"
      @task-added="handleTaskAdded"
      @task-deleted="handleTaskDeleted"
      @task-done="handleTaskDone"
      @task-title-changed="handleTitleChanged"
    />
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

header {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  margin-bottom: 2rem;
}

.filters {
  display: flex;
  gap: 1rem;
}
</style>
