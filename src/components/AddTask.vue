<template>
    <form @submit="onSubmit" class="add-form">
      <div class="form-control">
        <label>Task</label>
        <input type="text" name="text" v-model="text" placeholder="Add Task" />
      </div>
      <div class="form-control">
        <label>Date</label>
        <input
          v-model="date"
          type="date"
          name="date"
          placeholder="Add Date"
        />
      </div>
      <div class="form-control">
        <label>Time</label>
        <input
          v-model="time"
          type="time"
          name="time"
          placeholder="Add Time"
        />
      </div>
      <div class="form-control form-control-check">
        <label>Set Reminder</label>
        <input v-model="reminder" type="checkbox"  name="reminder" />
      </div>
  
      <input type="submit" value="Save Task" class="btn btn-block" />
    </form>
</template>

<script>
    export default {
        name: 'AddTask',
    data() {
        return {
            text: '',
            date:'',
            time: '',
            reminder:false
        }
    },
    methods: {
        onSubmit(e) {
            e.preventDefault()
            if(!this.text) {
                alert("Pls enter task")
                // return
            }

            function formatDate(dateString) {
              const date = new Date(dateString);
              const options = { month: 'long', day: 'numeric' };
              const formattedDate = date.toLocaleDateString('en-US', options);
              return formattedDate;
            }

            function convertTo12Hour(time24) {
              const time12 = new Date(`1970-01-01T${time24}:00`);
              return time12.toLocaleString('en-US', { hour: 'numeric', minute: 'numeric', hour12: true });
            }

            
            
            const formattedDate = formatDate(this.date)
            const formattedTime = convertTo12Hour(this.time);

            const newTask = {
                // id: Math.floor(Math.random() *100000),
                text: this.text,
                day: formattedDate + ' at ' + formattedTime,
                reminder: this.reminder
            }

            this.$emit('add-task',newTask)

            this.text=""
            this.day=""
            this.reminder=false


        }
    }
}
</script>


<style scoped>
.add-form {
  margin-bottom: 40px;
}

.form-control {
  margin: 20px 0;
}

.form-control label {
  display: block;
}

.form-control input {
  width: 100%;
  height: 40px;
  margin: 5px;
  padding: 3px 7px;
  font-size: 17px;
}

.form-control-check {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.form-control-check label {
  flex: 1;
}

.form-control-check input {
  flex: 2;
  height: 20px;
}
</style>