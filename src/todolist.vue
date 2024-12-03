<template>
  <div>
    <h1>TODO</h1>
    <h3>What needs to be done?</h3>
    <form @submit.prevent="addTask">
      <input v-model="newTaskName" placeholder="Enter a new task" required />
      <button type="submit" style="width: 100%; height: 50%; background-color: black; color: white;">
        Add Task
      </button>
    </form>

    <h3>{{ tasks.length }} tasks remaining</h3>

    <ul style="list-style: none; padding: 0;">
      <li 
        v-for="task in tasks" 
        :key="task.id" 
        style="display: flex; align-items: center; justify-content: space-between; margin-bottom: 10px;"
      >
        <!-- Checkbox -->
        <input 
          type="checkbox" 
          v-model="task.completed" 
          style="margin-right: 10px;" 
        />

        <!-- Task Name (Editable or Static) -->
        <div v-if="editTaskId === task.id" style="flex: 1; margin-right: 10px;">
          <input v-model="editTaskName" style="width: 100%;" />
        </div>
        <div v-else style="flex: 1; margin-right: 10px; text-decoration: task.completed ? 'line-through' : 'none';">
          {{ task.name }}
        </div>

        <!-- Action Buttons -->
        <div style="display: flex; gap: 10px;">
          <button 
            v-if="editTaskId === task.id" 
            @click="saveEdit(task)" 
            style="background-color: green; color: white;"
          >
            Save
          </button>
          <button 
            v-if="editTaskId === task.id" 
            @click="cancelEdit" 
            style="background-color: gray; color: white;"
          >
            Cancel
          </button>
          <button 
            v-else 
            @click="startEditing(task)" 
            style="background-color: blue; color: white;"
          >
            Edit
          </button>
          <button 
            @click="deleteTask(task)" 
            style="background-color: red; color: white;"
          >
            Delete
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  setup() {
    const newTaskName = ref('');
    const tasks = ref([]);
    const editTaskId = ref(null); // Tracks the ID of the task being edited
    const editTaskName = ref(''); // Holds the name of the task being edited

    // Add a new task
    const addTask = () => {
      if (!newTaskName.value.trim()) return;
      tasks.value.push({
        id: Date.now(), // Unique ID
        name: newTaskName.value,
        completed: false, // Checkbox for completion
      });
      newTaskName.value = '';
      saveTasks();
    };

    // Start editing a task
    const startEditing = (task) => {
      editTaskId.value = task.id;
      editTaskName.value = task.name;
    };

    // Save the edited task
    const saveEdit = (task) => {
      task.name = editTaskName.value;
      editTaskId.value = null;
      editTaskName.value = '';
      saveTasks();
    };

    // Cancel editing
    const cancelEdit = () => {
      editTaskId.value = null;
      editTaskName.value = '';
    };

    // Delete a task
    const deleteTask = (task) => {
      tasks.value = tasks.value.filter(t => t.id !== task.id);
      saveTasks();
    };

    // Save tasks to local storage
    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    };

    return {
      newTaskName,
      tasks,
      editTaskId,
      editTaskName,
      addTask,
      startEditing,
      saveEdit,
      cancelEdit,
      deleteTask,
    };
  },
};
</script>

<style>
h1, h3 {
  text-align: center;
}

input {
  padding: 10px;
  border: 2px solid #ccc;
  border-radius: 5px;
  width: 100%;
}

button {
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-top: 5px;
  display: flex;
  align-items: center;
}
</style>
