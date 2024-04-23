<template>
  <div id="app">
    <ProductListingPage />
  </div>
</template>

<script>
import ProductListingPage from './components/ProductListingPage.vue'
import '@fortawesome/fontawesome-free/css/all.css'

export default {
  name: 'App',
  components: {
    ProductListingPage
  }
}
</script>
<style src="./components/ProductListingPage.css" scoped></style>

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
