<template>
  <div id="app">
    <tasks-progress :progress="progress"></tasks-progress>
    <h1>Tarefas</h1>
    <new-task @taskAdded="addTask" />
    <task-grid @taskDeleted="deleteTask" :tasks="task" @taskStateChanged="toggleTaskState"></task-grid>
  </div>
</template>

<script>
import NewTask from "./components/NewTask.vue";
import TaskGrid from "./components/TaskGrid.vue";
import TasksProgress from './components/TasksProgress.vue';

export default {
  components: { TaskGrid, NewTask, TasksProgress },

  data() {
    return {
      task: [],
    };
  },
  computed:{
    progress(){
      const total = this.task.length
      const done = this.task.filter(t=>!t.pending).length

      return Math.round(done/total * 100) || 0
    }
  },
  methods: {
    addTask(task) {
      const sameName = (t) => t.name === task.name;
      const reallyNew = this.task.filter(sameName).length == 0;
      reallyNew &&
        this.task.push({
          name: task.name,
          pending: task.pending || true,
        });
    },
    deleteTask(i) {
      this.task.splice(i, 1);
    },
    toggleTaskState(i) {
      this.task[i].pending = !this.task[i].pending;
    },
  },
  created() {
    const json = localStorage.getItem('task')
    const array = JSON.parse(json)
    this.tasks = Array.isArray(array) ? array : []
  },
  watch:{
    tasks:{
      deep:true,
      handler(){
        localStorage.setItem('task',JSON.stringify(this.task))
      }
    }
  }
  
};
</script>

<style>
body {
  font-family: "Lato", sans-serif;
  background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
  color: #fff;
}

#app {
  display: flex;
  flex: 1;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

#app h1 {
  margin-bottom: 5px;
  font-weight: 300;
  font-size: 3rem;
}
</style>
