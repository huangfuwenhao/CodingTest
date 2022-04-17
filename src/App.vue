<template>
  <div id="app">
    <CodingTest />
    <!-- <div :style="{ display: 'flex', justifyContent: 'center' }">
      <pre :style="{ textAlign: 'left' }">
<code>{{ JSON.stringify(blocklet, null, 2) }}</code>
    </pre>
    </div> -->
  </div>
</template>

<script>
import CodingTest from './components/CodingTest.vue';

export default {
  name: 'App',
  components: {
    CodingTest,
  },
  data() {
    return {
      blocklet: window?.blocklet,
    };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
}
</style>
