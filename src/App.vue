<template>
	<div id="app">
		<h1>Tarefas</h1>
    <TasksProgress :progress="progress" />
		<NewTask @taskAdded="addTask" />
		<TaskGrid  :tasks="tasks" @taskDeleted="deleteTask" @taskStateChanged="toggleTaskState" 	 />
	</div>
</template>

<script>
import TasksProgress from "./components/TasksProgress.vue";
import TaskGrid from "./components/TaskGrid.vue";
import NewTask from "./components/NewTask.vue";

export default {
  // eslint-disable-next-line vue/no-unused-components
  components: { TasksProgress, TaskGrid, NewTask },
  data() {
    return {
      tasks: [],
    };
  },
  computed: {
    progress() {
      const total = this.tasks.length;
      const done = this.tasks.filter((t) => !t.pending).length;
      return Math.round((done / total) * 100) || 0;
    },
  },
  watch: {
    tasks: {
      // obj
      deep: true,
      handler() {
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
      },
    },
  },
  methods: {
    addTask(task) {
      const sameName = (t) => t.name === task.name; // compara se ja tem uma task cadastrada.
      const reallyNew = this.tasks.filter(sameName).length == 0; // filter [filtra] o elemento da função
      if (reallyNew) {
        this.tasks.push({
          name: task.name,
          pending: task.pending || true,
        });
        this.$notification.success({
          message: `Notification`,
          description: "TASK CADASTRADA COM SUCESSO.",
        });
      } else if (!reallyNew) {
        this.$notification.error({
          message: `Notification`,
          description: "NOME DA TASK JÁ EXISTE.",
        });
      }
    },
    deleteTask(i) {
      this.tasks.splice(i, 1);
        this.$notification.success({
          message: `Notification`,
          description: "TASK EXCLUIDA COM SUCESSO.",
        });
    },
    toggleTaskState(i) {
      this.tasks[i].pending = !this.tasks[i].pending;
    },
  },
  created() {
    const json = localStorage.getItem("tasks");
    const array = JSON.parse(json);
    this.tasks = Array.isArray(array) ? array : [];
  },
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
