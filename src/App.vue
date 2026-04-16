<script setup lang="ts">

import {ref,computed,watch,onMounted} from 'vue';
const newTask = ref("");

// Dummy data
const tasks = ref([
  {id:1,text: "call mom", completed: false, favorite: false },
  {id:2,text: "buy groceries", completed: false, favorite: false },
  {id:3,text: "finish project", completed: false, favorite: false },
  {id:4,text: "exercise", completed: false, favorite: false },
  {id:5,text: "read a book", completed: false, favorite: false },
]);

// Add task function
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

// Delete task function, takes id as parameter and filters out the task with that id from the tasks array.
function deleteTask(id: number){
  tasks.value = tasks.value.filter((t) => t.id !=id)
};
const editingId = ref(null);
const editingBuffer = ref("");
function startEdit(task: any){
  editingId.value = task.id;
  editingBuffer.value = task.text;
}

// cancel edit function, resets the editingId and editingBuffer to null and empty string respectively, effectively canceling the edit operation.
function cancelEdit(){
  editingId.value = null
  editingBuffer.value = ""
}
// Finish edit function, checks if the task being edited is the same as the one passed as a parameter. 
// If it is, it trims the editing buffer and checks if it's empty. If it's empty, it deletes the task; otherwise, 
// it updates the task's text with the trimmed value. Finally, it calls cancelEdit to reset the editing state.
function finishEdit(task: any){
  if(editingId.value !== task.id){
    return;
  }
  const trimmed = editingBuffer.value.trim()
  if(!trimmed){
    deleteTask(task)
  }else{
    task.text = trimmed
  }
function favoriteTask(task: any){
  task.favorite = !task.favorite
}
  cancelEdit();
}
const search = ref("");
const currentFilter = ref("all");
const filters = [
  {name: "all", label: "All"},
  {name: "active", label: "Active"},
  {name: "completed", label: "Completed"},
  {name: "favorite", label: "Favorite"},
];
// Filter task function, filters the tasks based on the current filter and search query. 
// It first filters the tasks based on the search query, then applies the selected filter
//  (active, completed, favorite) to further narrow down the results.
const filteredTasks = computed(() => {
  return tasks.value.filter((t) => t.text.toLowerCase().includes(search.value.toLowerCase()))
    .filter((t) => {
    if(currentFilter.value === "active"){
      return !t.completed;
    }else if(currentFilter.value === "completed"){
      return t.completed;
    }else if(currentFilter.value === "favorite"){
      return t.favorite;
    }
    return true;
  }).filter((t) => t.text.toLowerCase().includes(search.value.toLowerCase()));
});
// Watcher for tasks, watches the tasks array for changes and updates the local storage with the new tasks array whenever it changes.
watch(tasks, () => {
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
}, {deep: true});
// On mounted hook, retrieves the tasks from local storage when the component is mounted and sets the tasks array to the retrieved value if it exists.
onMounted(() => {
  const saved = localStorage.getItem("tasks");
  if(saved){
    tasks.value = JSON.parse(saved);
  }
});
</script>

<template>
 <div class="app">
  <h1>To Do App</h1>
  <div class="input-row">
    <input type="text" placeholder="search tasks" v-model="search"/>
    <select v-model="currentFilter">
      <option v-for="filter in filters" :key="filter.name" :value="filter.name">{{filter.label}}</option>
    </select>
  </div>
  <div class="input-row">
    <input type="text" placeholder="add task here" v-model="newTask"/>
    <button @click="addTask">Add</button>
  </div>
  <div>
    <ul class="task-list">
      <li v-for="task in filteredTasks" :key="task.id" :class="{done: task.completed, editing: editingId === task.id}">
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
