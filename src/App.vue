<script setup lang="ts">

import {ref} from 'vue';
const newTask = ref("");
const tasks = ref([
  {id:1,text: "call mom", completed: false, favorite: false },
  {id:2,text: "buy groceries", completed: false, favorite: false },
  {id:3,text: "finish project", completed: false, favorite: false },
  {id:4,text: "exercise", completed: false, favorite: false },
  {id:5,text: "read a book", completed: false, favorite: false },
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
const editingId = ref(null);
const editingBuffer = ref("");
function startEdit(task){
  editingId.value = task.id;
  editingBuffer.value = task.text;
}

function cancelEdit(){
  editingId.value = null
  editingBuffer.value = ""
}
function finishEdit(task){
  if(editingId.value !== task.id){
    return;
  }
  const trimmed = editingBuffer.value.trim()
  if(!trimmed){
    deleteTask(task)
  }else{
    task.text = trimmed
  }
function favoriteTask(task){
  task.favorite = !task.favorite
}
  cancelEdit();
}

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
      <li v-for="task in tasks" :key="task.id" :class="{done: task.completed, editing: editingId === task.id}">
        <template v-if="editingId === task.id">
          <input class="edit-input" type="text" v-model="editingBuffer"
           @keyup.enter="finishEdit(task)" @blur="finishEdit(task)"
           @keydown.esc="cancelEdit"/>
        </template>
        <template v-else>
          <button class="delete" @click="deleteTask(task.id)">X</button>
          <button class="favorite" @click="task.favorite = !task.favorite">{{task.favorite ? "★" : "☆"}}</button>
          <input type="checkbox" v-model="task.completed"/>
          <span @click="startEdit(task)">{{task.text}}</span>
        </template>
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
.edit-input {
  flex-grow: 1;
  padding: 0.5rem;
  border-radius: 6px;
  border: 1px solid #191616;
}
.favorite {
  background: none;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;
}
.favorite:hover {
  color: #f39c12;
}
</style>
