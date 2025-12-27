<template>
  <div id="app">
    <ProductDisplay />
  </div>
</template>

<script>
import ProductDisplay from './components/ProductDisplay.vue'

export default {
  name: 'App',
  components: {
    ProductDisplay
  }
}
</script>

<style>
/* Global styles sudah ada di page.css */
/* App.vue hanya merender ProductDisplay */
</style>
