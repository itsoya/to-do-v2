<script setup lang="ts">

import {ref} from 'vue';
const newTask = ref("");
const tasks = ref([
  {id:1,text: "call mom", completed: false, favorite: false },
]);
function addTask(){
  const text = newTask.value.trim()
  if(!text){return;}
  
  tasks.value.push({
    id: Date.now(),
    text: text,
    completed: false,
    favorite: false,

  });
  newTask.value=""
};
function deleteTask(id: number){
  tasks.value = tasks.value.filter((t) => t.id !=id)
};

</script>

<template>
 <div class="app">
  <h1>To Do App</h1>
  <div class="input-row">
    <input type="text" placeholder="add task here" v-model="newTask"/>
    <button @click="addTask">Add</button>
  </div>
  <div>
    <ul class="task-list">
      <li v-for="task in tasks" :key="task.id" :class="{done: task.completed}">
        <button class="delete" @click="deleteTask(task.id)">X</button>
        <input type="checkbox" v-model="task.completed"/>
        <span>{{task.text}}</span>
      </li>
    </ul>
  </div>
 </div>
</template>

<style scoped>
.app {
  max-width: 500px;
  margin: 2rem auto;
  font-family: sans-serif;
  text-align: center;
}
.input-row {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}
input {
  flex-grow: 1;
  padding: 0.5rem;
  border-radius: 6px;
  border: 1px solid #ccc;
}
button {
  padding: 0.5 rem 1rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  cursor: pointer;
}
.task-list{
  list-style: none;
  padding: 0px;
}
.task-list li {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem;
  border: 1px solid #eee;
}
.task-list li.done span{
  text-decoration: line-through;
  opacity: 0.6;
}
.delete {
  background: #e5e5e5;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 0.2rem 0.5rem;
  cursor: pointer;
}
.delete:hover{
  transition: 0.5s;
  background: #932d2d;
}

</style>
