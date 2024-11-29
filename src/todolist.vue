<template>
  <div>
    <h1>TODO</h1>
    <h3>What needs to be done?</h3>
    <form @submit.prevent="addTask">
      <input v-model="newTaskName" placeholder="Enter a new task" required />
    <button type="submit" style="width: 100%; height: 50%; background-color: black; color: white;">Add Task</button>

    </form>
    <div style="display: flex; justify-content: center; gap: 20px; margin-top: 20px;">
  <button @click="filter = 'all'">All</button>
  <button @click="filter = 'completed'">Completed</button>
  <button @click="filter = 'incomplete'">Incomplete</button>
</div>

    <ul>
      <li v-for="task in filteredTasks" :key="task.id" :class="['task', task.status]" :style="{ backgroundColor: getStatusColor(task.status) }">
        {{ task.name }} ({{ task.status }})
        <button @click="toggleCompletion(task)">Toggle Completion</button>
        <button @click="markAsCompleted(task)" v-if="task.status !== 'completed'">Mark as Completed</button>
        <button @click="deleteTask(task)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';

export default {
  setup() {
    const newTaskName = ref('');
    const tasks = ref([]);
    const filter = ref('all');

    
    onMounted(() => {
      const storedTasks = localStorage.getItem('tasks');
      if (storedTasks) {
        tasks.value = JSON.parse(storedTasks);
      }
    });

    const filteredTasks = computed(() => {
      switch (filter.value) {
        case 'all':
          return tasks.value;
        case 'completed':
          return tasks.value.filter(task => task.status === 'completed');
        case 'incomplete':
          return tasks.value.filter(task => task.status !== 'completed');
        default:
          return tasks.value;
      }
    });

    const addTask = () => {
      if (newTaskName.value.trim() === '') return; 
      const newTask = {
        id: tasks.value.length + 1, 
        name: newTaskName.value,
        status: 'pending' 
      };
      tasks.value.push(newTask);
      newTaskName.value = ''; 
      saveTasks(); 
    };

    const toggleCompletion = (task) => {
      task.status = task.status === 'completed' ? 'pending' : 'completed';
      saveTasks(); 
    };

    const markAsCompleted = (task) => {
      task.status = 'completed';
      saveTasks(); 
    };

    const deleteTask = (task) => {
      tasks.value = tasks.value.filter(t => t.id !== task.id); 
      saveTasks(); 
    };

    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    };

    const getStatusColor = (status) => {
      switch (status) {
        case 'pending':
          return '#FFC5C5'; 
        case 'in progress':
          return '#FFFFCC'; 
        case 'completed':
          return '#C6F4C6'; 
        default:
          return '#FFFFFF'; 
      }
    };

    return {
      newTaskName,
      tasks,
      filter,
      filteredTasks,
      addTask,
      toggleCompletion,
      markAsCompleted,
      deleteTask,
      getStatusColor
    };
  }
}
</script>

<style>
.task {
  padding: 10px;
  border: 1px solid;
  margin-bottom: 10px;
  
  
}

.pending {
  border-color: red;
}

.in-progress {
  border-color: yellow;
}

.completed {
  border-color: green;
}
h1{
  text-align: center;
}
h3{
  text-align: center;
}
input{
  border: 3px solid ;
  width: 100%;
  height: 50px ;
}
button type{
  width: 50%;
}
</style>


