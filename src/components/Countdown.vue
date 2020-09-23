<template>
  <div class="countdownvue">

    <div class="prefix" v-if="prefix">{{prefix}}</div>
    <div class="time" v-if="time">{{time}}</div>
    <div class="description" v-if="description">{{description}}</div>
    
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class Countdown extends Vue {
  @Prop() private prefix!: string;
  @Prop() private time!: string;
  @Prop() private description!: string;
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.time{
  font-family: monospace;
  
}
.time,.prefix{
  font-size: 12rem;
  display: inline-block;
}
.description{
  font-size: 5rem;
}
</style>
