<template>
  <div id="app">
    <Table/>
      <div>
      <p>
        {{counter}}
      </p>
      <button type="submit" @click="adding()">Add</button>
    </div>
  </div>
</template>

<script>
import Table from './components/Table.vue'
import {mapState} from 'vuex'

export default {
  name: 'App',
  components: {
    Table,
    
  },
  computed: {
      ...mapState({
      counter: state => state.count
    }),
  },
  methods: {
    adding() {
      this.$store.commit('increment')
    }
  }
  
}
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
