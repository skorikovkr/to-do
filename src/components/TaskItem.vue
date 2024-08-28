<script setup>
import { inject, ref } from 'vue'
import NewTaskForm from './NewTaskForm.vue'

const props = defineProps({
  task: Object,
  includeSubTasks: {
    type: Boolean,
    default: true
  }
})

const taskFormVisible = ref(false)

const emits = defineEmits(['taskAdded', 'taskDeleted', 'taskDone', 'taskTitleChanged'])

const tasks = inject('tasks')

const handleDeleteClick = () => {
  emits('taskDeleted', props.task)
}

const handleDoneClick = () => {
  emits('taskDone', props.task)
}

const handleShowTaskForm = () => {
  taskFormVisible.value = !taskFormVisible.value
}

const handleTitleChange = (title) => {
  emits('taskTitleChanged', props.task, title)
}

const handleTaskAdded = (newTask) => {
  emits('taskAdded', newTask)
  taskFormVisible.value = false
}
</script>

<template>
  <div class="task-item">
    <div class="task-item__controls">
      <button @click="handleDoneClick">
        <img v-if="task.done" src="./../assets/check-icon.svg" height="15px" />
        <img v-else src="./../assets/square.svg" height="15px" />
      </button>
      <input :value="task.title" @input="(e) => handleTitleChange(e.target.value)" />
      <button @click="handleDeleteClick">
        <img src="./../assets/delete-icon.svg" height="15px" />
      </button>
      <button @click="handleShowTaskForm">
        <img src="./../assets/plus.svg" height="15px" />
      </button>
    </div>
    <div class="task-item__form" v-show="taskFormVisible">
      <NewTaskForm :parent-id="task.id" @task-added="handleTaskAdded" />
    </div>
    <div class="task-item__sub-tasks" v-if="includeSubTasks">
      <TaskItem
        v-for="st in tasks.filter((t) => t.parentId === task.id)"
        :task="st"
        :key="st.id"
        @task-added="(newSubTask) => emits('taskAdded', newSubTask)"
        @task-deleted="(subTask) => emits('taskDeleted', subTask)"
        @task-done="(subTask) => emits('taskDone', subTask)"
        @task-title-changed="(subTask, title) => emits('taskTitleChanged', subTask, title)"
      />
    </div>
  </div>
</template>

<style scoped>
.task-item {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.task-item__controls {
  margin-left: 1rem;
  display: flex;
  gap: 0.25rem;
}

.task-item__sub-tasks {
  margin-left: 1rem;
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.task-item__form {
  margin-left: 1rem;
}
</style>
