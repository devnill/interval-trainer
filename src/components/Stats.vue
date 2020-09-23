<template>
  <div class='stats'>
    <div v-if="elapsed">Time Elapsed: {{elapsed}}</div>
    <div v-if="remaining">Time Remaining: {{remaining}}</div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Stats extends Vue {
  @Prop() private elapsed!: String;
  @Prop() private remaining!: String;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.stats{
  font-size: 3rem;
  position: absolute;
  bottom:0;
  width:100%;
  right: 0;
  text-align: right;
}
</style>




