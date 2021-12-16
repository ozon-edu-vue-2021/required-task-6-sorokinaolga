<template>
  <div id="app">
    <table-component />
  </div>
</template>

<script>

import TableComponent from '@/components/table-component';
export default {
  name: 'App',
  components: {
    TableComponent
  }
};
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
