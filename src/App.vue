// Main Vue Component
<script setup>
import { ref, watch } from 'vue';
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap';

// Task data structure
const taskGroups = ref({
  High: [],
  Medium: [],
  Low: []
});

// Load tasks from localStorage on initialization
const loadTasks = () => {
  const savedTasks = JSON.parse(localStorage.getItem('priorityTasks'));
  if (savedTasks) {
    taskGroups.value = savedTasks;
  }
};

loadTasks();

// Watcher to persist tasks in localStorage
watch(taskGroups, (newTasks) => {
  localStorage.setItem('priorityTasks', JSON.stringify(newTasks));
}, { deep: true });

// Task form handling
const taskName = ref('');
const taskPriority = ref('High');

const addTask = () => {
  if (taskName.value.trim() !== '') {
    taskGroups.value[taskPriority.value].push(taskName.value.trim());
    taskGroups.value[taskPriority.value].sort();
    taskName.value = '';
  }
};

// Delete task functionality
const deleteTask = (priority, index) => {
  taskGroups.value[priority].splice(index, 1);
};
</script>

<template>
  <div class="container py-5">
    <h1 class="text-center mb-4">Priority Task List</h1>

    <!-- Task Form -->
    <form @submit.prevent="addTask" class="row g-3 mb-5">
      <div class="col-md-7">
        <input
          v-model="taskName"
          type="text"
          class="form-control"
          placeholder="Task Name"
          required
        />
      </div>
      <div class="col-md-3">
        <select v-model="taskPriority" class="form-select">
         <option value="High">High</option>
          <option value="Medium">Medium</option>
          <option value="Low">Low</option>
        </select>
      </div>
      <div class="col-md-2">
        <button type="submit" class="btn btn-primary w-100">Add Task</button>
      </div>
    </form>

    <!-- Task Groups -->
    <div class="task-groups row">
      <div v-for="(tasks, priority) in taskGroups" :key="priority" class="col-md-12 mb-4">
        <div class="card">
          <div class="card-header bg-secondary text-white">
            <h2 class="h5 mb-0 text-center">{{ priority }} Priority</h2>
          </div>
          <div class="card-body">
            <ul class="list-group list-group-flush">
              <li v-for="(task, index) in tasks" :key="index" class="list-group-item d-flex justify-content-between align-items-center">
                <span>{{ task }}</span>
                <button @click="deleteTask(priority, index)" class="btn btn-danger btn-sm">Delete</button>
              </li>
              <li v-if="tasks.length === 0" class="list-group-item text-muted text-center">No tasks available in this category</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  max-width: 1200px;
}

h1 {
  font-size: 2.5rem;
  font-weight: 700;
}

.task-groups {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.card {
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
}

.card-header {
  font-weight: bold;
  font-size: 1.2rem;
  text-transform: uppercase;
}

.list-group-item {
  font-size: 1rem;
}

.text-muted {
  font-style: italic;
}
</style>
