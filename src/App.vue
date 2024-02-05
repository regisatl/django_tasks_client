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
<style lang="scss">
@import "./assets/css/output";
@import "./assets/css/tailwind";
</style>