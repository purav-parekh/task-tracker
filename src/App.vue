<template>
  <div class="container">
    <Header title="Task Tracker" @toggle-add-task="toggleAddTask" :showAddTask="showAddTask"/>
    <div>
      <AddTask v-if="showAddTask" @add-task = "addTask" />
    </div>
    <Tasks @delete-task="deleteTask"  @toggle-reminder="toggleReminder" :tasks="tasks"/>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Tasks from './components/Tasks.vue'
import AddTask from './components/AddTask.vue'



export default {
  name: 'App',
  components: {
    Header,
    Tasks,
    AddTask
  },
  methods: {
    async fetchTasks () {
      const res = await fetch("http://localhost:5000/tasks")
      const data = await res.json()
      return data
    },
    async getTask(id) {
      const res = await fetch(`http://localhost:5000/tasks/${id}`)
      const data = await res.json()
      return data
    },
    async addTask(newTask) {
      const res = await fetch('http://localhost:5000/tasks', {
        method: 'POST',
        headers: {
          'Content-type':'application/json',
        },
        body: JSON.stringify(newTask)
      })

      const data = await res.json()
      this.tasks = [...this.tasks,data]
    },
    async deleteTask(id) {
      const res = await fetch(`http://localhost:5000/tasks/${id}`,{
        method: 'DELETE',
      })
      res.status === 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id)) : alert('Error Deleting')
    },
    async toggleReminder(id) {
      const taskToToggle = await this.getTask(id)
      const updatedTask = {...taskToToggle, reminder: !taskToToggle.reminder}
      const res = await fetch(`http://localhost:5000/tasks/${id}`,{
        method: 'PUT',
        headers: {
          'Content-type': "application/json"
        },
        body: JSON.stringify(updatedTask)
      })
      const data = await res.json()
      
      this.tasks.forEach((task) => {
        if(task.id === id) {
          task.reminder = data.reminder
        }
      })
    },
    
    toggleAddTask() {
      this.showAddTask = !this.showAddTask
    },
    
  },
  data() {
    return {
      tasks: [],
      showAddTask: true,
    }
  },
  async created() {
    this.tasks = await this.fetchTasks()
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Poppins', sans-serif;
}

.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}

.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}

.btn:focus {
  outline: none;
}

.btn:active {
  transform: scale(0.98);
}

.btn-block {
  display: block;
  width: 100%;
}
</style>
