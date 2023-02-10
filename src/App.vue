<template>
  <header>
    <div id="header-title">&laquo; PROXY CHECKER &raquo;</div>
  </header>

  <div id="left"></div>

  <div id="center">
    <proxies :api="api"></proxies>
  </div>

  <div id="right"></div>

  <footer>
    (C) 2023
  </footer>
</template>

<script>
import Proxies from "@/components/Proxies.vue";

export default {
  name: 'App',
  components: {
    Proxies
  },

  data() {
    return {
      api: {
        route: import.meta.env.VITE_API_V1,
      }
    }
  }
}
</script>

<style scoped>

</style>
