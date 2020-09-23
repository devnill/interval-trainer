<template>
  <ul class='heat-list'>
    <li v-for="(heat, idx) in heats" :key="idx">
      {{ heat.description }} {{heat.duration/1000}}s
    </li>
  </ul>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component
export default class HeatList extends Vue {
  @Prop() private heats: object[];
  @Prop() private current!: number;
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




