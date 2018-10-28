<template>
  <div id="app" :style="`background-color: ${backgroundColor};`">
    <Alert v-show="unsupportedBrowser">
      Uh-oh! Your browser doesn't support web audio.
      Don't worry, you can upgrade it
      <a href="https://browsehappy.com/"
         target="_blank"
         rel="noopener noreferrer">
         here</a>.
    </Alert>

    <Tuner @unsupported-browser="onUnsupportedBrowser"
           @cents-changed="onCentsChanged" />
  </div>
</template>

<script>
import Alert from './components/Alert.vue'

import Tuner from './components/Tuner.vue'

export default {
  components: {
    Alert,
    Tuner
  },
  data () {
    return {
      backgroundColor: '#2c0',
      unsupportedBrowser: false
    }
  },
  methods: {
    onUnsupportedBrowser () {
      this.unsupportedBrowser = true
    },
    onCentsChanged (cents) {
      this.backgroundColor = Math.abs(cents) > 10 ? '#c20' : '#2c0'
    }
  }
}
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  flex-shrink: 0;
  height: 100vh;
}

body {
  color: #eee;
  margin: 0;
  font-family: 'Raleway', sans-serif;
  user-select: none;
}
</style>
