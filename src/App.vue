<template>
  <v-app>
      <feed/>
  </v-app>
</template>

<script>
import Feed from './components/Feed'

export default {
  name: 'App',
  components: {
    Feed
  },
  data () {
    return {
      //
    }
  }
}
</script>
