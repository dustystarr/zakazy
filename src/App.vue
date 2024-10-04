<template>
  <div id="app">
    <ProductList />
  </div>
</template>

<script>
import ProductList from './components/ProductList.vue'

export default {
  name: 'App',
  components: {
    ProductList
  }
};
</script>

<style>
#app {
  display: flex;
  justify-content: center;
  padding: 20px;
  background-color: #f5f5f5;
  min-height: 100vh;
}
</style>
