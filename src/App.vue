<template>
  <div class="container">
    <h1>TODO</h1>
    <h3>What needs to be done?</h3>
    <form @submit.prevent="addTask" class="task-form">
      <input 
        v-model="newTaskName" 
        placeholder="Enter a new task" 
        class="new-task-input" 
        required 
      />
      <button type="submit" class="add-task-btn">
        Add Task
      </button>
    </form>

    <h3>{{ tasks.length }} tasks remaining</h3>
    <p v-if="tasks.length >= 10" style="color: red;">Task limit reached. Please delete a task to add more.</p>

    <ul class="task-list">
      <li 
        v-for="task in tasks" 
        :key="task.id" 
        class="task-item"
      >
        <!-- Checkbox -->
        <input 
          type="checkbox" 
          v-model="task.completed" 
          class="task-checkbox" 
        />

        <!-- Task Name (Editable or Static) -->
        <div v-if="editTaskId === task.id" class="edit-task">
          <input v-model="editTaskName" class="edit-input" />
        </div>
        <div v-else class="task-name" :style="{ textDecoration: task.completed ? 'line-through' : 'none' }">
          {{ task.name }}
        </div>

        <!-- Action Buttons -->
        <div class="action-buttons">
          <button 
            v-if="editTaskId === task.id" 
            @click="saveEdit(task)" 
            class="save-btn"
          >
            Save
          </button>
          <button 
            v-if="editTaskId === task.id" 
            @click="cancelEdit" 
            class="cancel-btn"
          >
            Cancel
          </button>
          <button 
            v-else 
            @click="startEditing(task)" 
            class="edit-btn"
          >
            Edit
          </button>
          <button 
            @click="deleteTask(task)" 
            class="delete-btn"
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
      if (tasks.value.length >= 10) {
        alert('You can only have up to 10 tasks at a time.');
        return;
      }
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
      task.name = editTaskName.value.trim();
      if (!task.name) return alert('Task name cannot be empty.');
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


<style scoped>
.container {
  max-width: 500px;
  margin: 0 auto; /* Center the container */
  text-align: center;
}

.task-form {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
}

.new-task-input {
  width: 100%;
  padding: 12px;
  border: 2px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

.add-task-btn {
  width: 100%;
  height: 50px;
  background-color: black;
  color: white;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
}

.task-list {
  list-style: none;
  padding: 0;
  margin-top: 20px;
}

.task-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.task-checkbox {
  margin-right: 10px;
}

.task-name {
  flex: 1;
  margin-right: 10px;
}

.edit-task {
  flex: 1;
  margin-right: 10px;
}

.edit-input {
  width: 100%;
  padding: 12px;
  border: 2px solid #6c63ff; /* Highlight border */
  border-radius: 8px;
  background-color: #f4f4f4;
  font-size: 16px;
}

.edit-input:focus {
  border-color: #4c4cff;
  box-shadow: 0 0 5px rgba(0, 0, 255, 0.5);
}

.action-buttons button {
  margin-left: 5px;
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  font-size: 14px;
  cursor: pointer;
}

.save-btn {
  background-color: green;
  color: white;
}

.cancel-btn {
  background-color: gray;
  color: white;
}

.edit-btn {
  background-color: blue;
  color: white;
}

.delete-btn {
  background-color: red;
  color: white;
}
</style>
