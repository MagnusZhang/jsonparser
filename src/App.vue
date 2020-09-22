<template>
  <div id="app">
    <div style="color:#f300fc;font-size:24px; width:100vw; text-align:center;">Simple JSON Parser</div>
    <JSONParser/>
  </div>
</template>

<script>
import JSONParser from './components/JSONParser.vue'

export default {
  name: 'App',
  components: {
    JSONParser
  }
}
</script>

<style>
#app {
  margin-top: 60px;
  padding-left:20px;
  width: 90vw;
  background:white;
  display:flex;
  flex-direction:column;
}
</style>
