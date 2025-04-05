<script setup>
import HomePage from './components/HomePage.vue'
</script>

<template>
  <HomePage />
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  width: 100%;
  min-width: 100%;
  max-width: 100vw;
  overflow-x: hidden;
  margin: 0;
  padding: 0;
}

body {
  margin: 0;
  padding: 0;
  background-color: #f5f7fa;
  font-family: 'PingFang SC', 'Helvetica Neue', Arial, sans-serif;
}

#app {
  width: 100vw;
  max-width: 100%;
  overflow-x: hidden;
  min-height: 100vh;
  margin: 0;
  padding: 0;
}
</style>
