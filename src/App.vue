<template>

  <Menu></Menu>
  <router-view/>
  <Footer></Footer>
</template>

<script>
import Menu from '@/components/Menu.vue'
import Footer from '@/components/Footer.vue'
export default {
  name: 'App',
  props: {
    msg: String
  },
  components:{
    Menu,
    Footer
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  
  color: #2c3e50;
}

</style>