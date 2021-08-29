<template>
  <div class="container">
    <h2 class="text-center mt-5">My Vue ToDo App</h2>
    <!-- INPUT -->
    <div class="d-flex">
      <input v-model="task" type="text" placeholder="Enter task" class="form-control">
      <button @click="submitTask" class="btn btn-warning rounded-0">Submit</button>
    </div>
    <!-- Task table -->
    <table class="table table-bordered mt-5">
      <thead>
        <tr>
          <th scope="col">task</th>
          <th scope="col">status</th>
          <th scope="col" class="text-center">Update</th>
          <th scope="col" class="text-center">Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>
            <span :class="{'finished': task.status === 'finished'}">{{task.name}}
            </span>
          </td>
          <td style="width:120px">
            <span class="pointer" @click="changeStatus(index)" 
              :class="{'text-danger':task.status === 'to-do',
              'text-warning': task.status === 'in-progress',
              'text-success': task.status === 'finished'
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
import Vue from 'vue';
import VueAxios from 'vue-axios';
import axios from 'axios';
Vue.use(VueAxios,axios);
export default {
  name: 'TodoApp',
  props: {
    msg: String
  },
  data(){
    return{
      task:"",
      editedTask: null,
      availableStatuses: ['to-do', 'in-progress', 'finished'],

      tasks:[
        {
          name: null,
          status: null
        }
      ]
    }
  },
  methods:{
    submitTask(){
      this.axios.post('http://localhost:3000/tasks',this.tasks).then((result)=>{
        console.log (result);
            if(this.task.length === 0) return;

          if (this.editedTask === null) {
            this.tasks.push({
              name: this.task,
              status: "to-do"
            });
          }else{
            this.tasks[this.editedTask].name = this.task;
            this.editedTask = null;
          }
        
          this.task = '';
      });
    },
    deleteTask(index){
      this.tasks.splice(index, 1);
    },
    editTask(index){
      this.task = this.tasks[index].name;
      this.editedTask = index;
    },
    changeStatus(index){
      let newIndex = this.availableStatuses.indexOf(this.tasks[index].status);
      if (++newIndex > 2) newIndex = 0;
      this.tasks[index].status = this.availableStatuses[newIndex];
    }
    
  },
  mounted(){
        Vue.axios.get('http://localhost:3000/tasks')
        .then(resp=>{
            this.tasks = resp.data;
           console.warn(resp);
        })
    }
}
</script>

<style scoped>
.pointer{
  cursor: pointer;
}
.finished{
  text-decoration: line-through;
}
</style>
