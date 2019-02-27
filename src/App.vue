<template>
  <div>
    <app-header></app-header>
    <feed/>
  </div>
</template>

<script>
import Feed from    './components/Feed'
import AppHeader from  './components/AppHeader'

export default {
  name: 'App',
  components: {
    Feed, 
    AppHeader
  },
  data () {
    return {
      //
    }
  }
}
</script>
