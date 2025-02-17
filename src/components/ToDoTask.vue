<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todoTasks = ref([]);
const listName = ref('');
const inputContent = ref('');

const todoTaskAsc = computed(() => {
  return [...todoTasks.value].sort((a, b) => b.createdAt - a.createdAt);
});

console.log(todoTaskAsc);
const addTask = () => {
  if (inputContent.value.trim() === '') {
    return
  }
  todoTasks.value.push({
    content: inputContent.value,
    done: false,
    createdAt: new Date().getTime()
  })
  inputContent.value = ""
};

const removeTask = task => {
  todoTasks.value = todoTasks.value.filter(t => t !== task)
}

watch(todoTasks, newVal => {
  localStorage.setItem('todoTasks', JSON.stringify(newVal))
}, { deep: true });

watch(listName, (newVal) => {
  localStorage.setItem('listName', newVal)
})

onMounted(() => {
  listName.value = localStorage.getItem('listName') || ''
  todoTasks.value = JSON.parse(localStorage.getItem('todoTasks')) || []
});
</script>

<template>
  
  <main class="app">
    <section class="app__title-section">
      <h1 class="app__title-section__title">
        <input placeholder="Enter todo list name here" type="text"  v-model="listName" />
      </h1>
    </section>
    <section class="app__create-task">
      <h2>
        Create a Task
      </h2>
      <form @submit.prevent="addTask">
        <p>
          What's on your todo list?
          <input placeholder="Enter your task" type="text" v-model="inputContent" />
        </p>
        <input type="submit" value="Add task" />
      </form>
    </section>
    <section class="app__todo-list-section">
      <h3>
        TODO LIST
      </h3>
      <div class="app__todo-list-section__todo-list">
        <div v-for="task in todoTaskAsc" :key="task.createdAt" :class="['task-item', { done: task.done }]">
          <label>
            <input type="checkbox" v-model="task.done" />
          </label>
          <div class="app__todo-list-section__todo-list__task">
            <input type="text" v-model="task.content">
          </div>
          <button class="app__todo-list-section__todo-list__delete-button" @click="removeTask(task)">
            Delete
          </button>
        </div>
      </div>
    </section>
  </main>
</template>