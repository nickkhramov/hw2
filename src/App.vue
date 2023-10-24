<template>
  <div id="app">
    <PokemonTable/>
  </div>
</template>

<script>
import PokemonTable from './components/PokemonTable.vue'

export default {
  name: 'App',
  components: {
    PokemonTable,

}
}
</script>

<style lang="css">
@import "./assets/styles/index.css"
</style>
