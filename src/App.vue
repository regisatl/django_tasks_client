<template>
  <div id="app">
    <TasksList />
  </div>
</template>

<script>
import TasksList from "./components/Tasks.vue";

export default {
  name: "App",
  components: {
    TasksList,
  },
};
</script>

<style>
@import "./assets/css/output.css";
</style>
