<template>
  <div class="container">
    <h2 class="text-center mt-5">TO DO App</h2>

    <div class="d-flex mt-5">
      <input v-model="task" type="text" placeholder="Enter task" class="form-control">
      <button @click="submitTask" class="btn btn-danger rounded-0">Add</button>
    </div>

    <!-- task table -->

    <table class="table table-bordered mt-5">
      <thead>
        <tr>
          <th scope="col">Task</th>
          <th scope="col">Status</th>
          <th scope="col" class="text-center">Edit</th>
          <th scope="col" class="text-center">Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>
            <span :class="{'finished': task.status === 'Finished'}">{{task.name}}</span>
            </td>
          <td style="width: 100px">
            <span @click="changeStatus(index)" class="pointer"
              :class="{'text-danger': task.status === 'To-do',
              'text-success': task.status === 'Finished'
              }"
            >
              {{task.status}}
            </span>
          </td>
          <td>
            <div class="text-center" @click="editTask(index)">
              <span class="fa fa-pen"></span>
            </div>
          </td>
          <td>
            <div class="text-center" @click="deleteTask(index)">
              <span class="fa fa-trash"></span>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

  </div>
</template>

<script>

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },

  data() {
    return {
      task: '',
      editedTask: null,
      availableStatus: ['To-do', 'Finished'],

      tasks: [
        {
          name: 'This is an example task, you can delete and edit this task!',
          status: 'To-do'
        }
      ]
    }
  },

  mounted(){
      fetch('http://localhost:8081/')
      .then(response => response.json())
      .then(data => {
        this.tasks = data;
      });
    },

  methods: {
    submitTask(){
      if(this.task.length === 0) return;

      if(this.editedTask === null){
        this.tasks.push({
        name: this.task,
        status: 'To-do'
      })

      fetch('http://localhost:8081/addtask', {
        method: 'post',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({
           name: this.task
        })
      })

      } else {
        this.tasks[this.editedTask].name = this.task;
        console.log( 'index , task' , this.editedTask, this.task)
        
        fetch('http://localhost:8081/edittask', {
          method: 'post',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify({
            index: this.editedTask,
            name: this.task
          })
        });

        this.editedTask = null;
      }
      
      this.task = '';      
    },

    deleteTask(index){
      this.tasks.splice(index, 1);
      
      fetch('http://localhost:8081/deletetask', {
        method: 'post',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({
          index: index
        })
      });

    },

    editTask(index){
      this.task = this.tasks[index].name;
      this.editedTask = index;
    },

    changeStatus(index){
      let newIndex = this.availableStatus.indexOf(this.tasks[index].status);
      if(++newIndex > 1) newIndex = 0;
      this.tasks[index].status = this.availableStatus[newIndex];

      fetch('http://localhost:8081/editstatus', {
          method: 'post',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify({
            index: index,
            status: this.availableStatus[newIndex]
          })
        });

    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.pointer {
  cursor: pointer;
}
.finished {
  text-decoration: line-through;
}
</style>
