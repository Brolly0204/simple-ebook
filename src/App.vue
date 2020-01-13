<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<style lang="css">
 #app {
   width: 100%;
   height: 100%;
 }
</style>

<script>
export default {
  name: 'App'
}
document.addEventListener('DOMContentLoaded', () => {
  const html = document.documentElement
  const fontSize = Math.min(50, window.innerWidth / 10)
  html.style.fontSize = fontSize + 'px'
})
</script>
