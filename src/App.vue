<script>
import Map from './components/Map.vue'
import Headermap from './components/header.vue'

export default {
  components: {
    Map,
    Headermap,
  }
}
</script>

<template>
  <Headermap />
  <main>
    <Map />
  </main>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
