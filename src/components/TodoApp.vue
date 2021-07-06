<template>
  <div class="container">
    <h2 class="text-center mt-5">First TO DO App</h2>

    <div class="d-flex">
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

const STORAGE_KEY = 'todo-storage';

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
          name: 'This is an example task, you can delete this if you want!!',
          status: 'To-do'
        }
      ]
    }
  },

  created(){
    this.tasks = JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]');
  },

  methods: {
    submitTask(){
      if(this.task.length === 0) return;

      if(this.editedTask === null){
        this.tasks.push({
        name: this.task,
        status: 'To-do'
      })
      }else{
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      }
      
      this.task = '';

      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
    },

    deleteTask(index){
      this.tasks.splice(index, 1);
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
    },

    editTask(index){
      this.task = this.tasks[index].name;
      this.editedTask = index;
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.tasks));
    },

    changeStatus(index){
      let newIndex = this.availableStatus.indexOf(this.tasks[index].status);
      if(++newIndex > 1) newIndex = 0;
      this.tasks[index].status = this.availableStatus[newIndex];
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
