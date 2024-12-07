<template>
  <TodoList :todoLists="todoLists" @update:todoLists="updateTodoLists" />
</template>

<script setup>
import { ref } from 'vue';
import TodoList from './components/TodoList.vue';

const todoLists = ref([]);

const updateTodoLists = (newTodoLists) => {
  todoLists.value = newTodoLists;
};
</script>