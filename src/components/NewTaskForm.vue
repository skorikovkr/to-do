<script setup>
import { defineProps, defineEmits, ref, unref } from 'vue'
import { uuidv4 } from '../../misc'

const props = defineProps({
  parentId: String
})

const emits = defineEmits(['taskAdded'])

const title = ref('')

const handleTaskAdd = () => {
  emits('taskAdded', {
    parentId: props.parentId,
    title: unref(title),
    done: false,
    id: uuidv4()
  })
  title.value = ''
}
</script>

<template>
  <form class="task-form" @submit.prevent="handleTaskAdd">
    <label for="title">Title:</label>
    <input name="title" v-model="title" />
    <input type="submit" value="Add" />
  </form>
</template>

<style scoped>
.task-form {
  display: flex;
  gap: 1rem;
}
</style>
