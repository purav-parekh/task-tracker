<template>
    <AddTask v-if="showAddTask" @add-task = "addTask" />
    <Tasks @delete-task="deleteTask"  @toggle-reminder="toggleReminder" :tasks="tasks"/>
</template>

<script>
import Tasks from '../components/Tasks.vue'
import AddTask from '../components/AddTask.vue'

export default {
  name: 'Home',
  components: {
    Tasks,
    AddTask
  },
  props: {
    showAddTask: Boolean
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
  },
  data() {
    return {
      tasks: [],
    }
  },
  async created() {
    this.tasks = await this.fetchTasks()
  },
}
</script>