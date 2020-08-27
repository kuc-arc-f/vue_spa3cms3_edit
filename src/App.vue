<template>
  <div id="app">
    <!--  
    class="container" -->
    <navbar />
    <div class="container">
      <router-view/>
    </div>
    <Footer />
  </div>
</template>

<!-- -->
<script>
import navbar from './components/Layouts/Navbar'
import Footer from './components/Layouts/Footer'

//
export default {
  name: 'app',
  components: {
    navbar, 
    Footer,
  }
}
</script>

<style>
</style>
