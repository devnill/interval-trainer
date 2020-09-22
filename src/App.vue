<template>
  <div id="app">
    <!--
      <img alt="Vue logo" src="./assets/logo.png">
      <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
    -->
    <div class='countdown' v-if="timeElapsed<0">
      Starting in {{formatTime(Math.abs(timeElapsed))}}

    </div>
    <div class='yay' v-else-if="workoutDone">
      YOU DID IT!!!
    </div>
    <div v-else-if="timeElapsed">
      <div class='countdown'>{{formattedTimeLeftInHeat}}</div>
      <div class='title'>{{currentHeat.description}}</div>
      <div class='stats'>
        <div>Time Elapsed: {{formattedTimeElapsed}}</div>
        <div>Time Remaining: {{formattedTimeLeftOverall}}</div>
      </div>
    </div>
    <div v-if="timeElapsed === null ||timeElapsed < 0 || workoutDone">
    <button @click="start">start</button>
    <button @click="reset">reset</button>
    <div>Start in <input type="number" v-model="startIn" /> seconds</div>
    </div>
    <div v-else>
    <button @click="reset">reset</button></div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
// import HelloWorld from './components/HelloWorld.vue';

@Component({
  methods:{
    start(){
      if(this.startTime === null){
        this.startTime = Date.now() + (this.startIn*1000);
        this.updateClock();
      }
    },
    reset(){
      this.startTime = null;
    },
    updateClock(){
      requestAnimationFrame(()=>{
        this.timeElapsed = this.startTime === null ? null : Date.now() - this.startTime;
        if(this.timeElapsed){
          this.updateClock();
        }
      })
    },
    formatTime(timestamp, showMs=true) {
      const time = new Date(timestamp|| 0);
      const ms = time.getUTCMilliseconds();
      const seconds =  time.getUTCSeconds();
      const minutes =  time.getUTCMinutes();
      const hours =  time.getUTCHours();
      
      let formattedTime = seconds.toString().padStart(2,'0')
      if(showMs){
        formattedTime = `${formattedTime}:${ms.toString().substr(0,2)}`
      }
      formattedTime = `${minutes.toString().padStart(2,'0')}:${formattedTime}`
      if(hours){
        formattedTime = `${hours.toString().padStart(2,'0')}:${formattedTime}`
      }
      return formattedTime;
    },

    heatStartTime(heatIndex){
      const heatsToCount = this.plan.slice(0,heatIndex)
      const startTime = heatsToCount.reduce((acc, cur)=>{
        return acc+cur.duration;
      },0);
      return startTime;
    }
  },
  computed: {
    overallDuration(){
      return this.plan.reduce((acc, cur)=>{
        return acc+cur.duration;
      },0);
    },
    workoutDone(){
      return this.timeElapsed && this.timeElapsed > this.overallDuration
    },
    formattedTimeElapsed(){
      return this.formatTime(this.timeElapsed, false)
    },
    formattedTimeLeftInHeat(){
      return this.formatTime(this.timeLeftInHeat)
    },
    formattedTimeLeftOverall(){
      return this.formatTime(this.timeLeftOverall, false) 
    },
    timeLeftOverall(){
      return this.overallDuration - this.timeElapsed;
    },
    currentHeat(){
      if( this.currentHeatIndex !== -1){ 
        const currentHeat = this.plan[this.currentHeatIndex];
        const startTime = this.heatStartTime(this.currentHeatIndex)
        const timeFormatting = this.timeElapsed !== null ? {
          startTime,
          endTime: startTime + currentHeat.duration
        } : {}; 
        return Object.assign(timeFormatting, currentHeat);
      }
      return null;
    },
    timeLeftInHeat(){
      if(!this.timeElapsed){return null} 

      return  this.currentHeat.endTime - this.timeElapsed
    },
    currentHeatIndex(){
      if(!this.startTime){
        return null;
      }
      const nextHeatIndex = this.plan.findIndex((heat, idx)=>{
        return this.heatStartTime(idx) > this.timeElapsed
      })
      if(nextHeatIndex === -1){
        return this.plan.length - 1;
      }
      return nextHeatIndex - 1;
    }
  },
  data(){
    return {
      startTime: null,
      timeElapsed: null,
      startIn: 30,
      plan: [
        /*{
          duration: 2*1000,
          description: 'test 2 s'
        },
        {
          duration: 3*1000,
          description: 'test 5s'
        },
        {
          duration: 5*1000,
          description: 'test 10s'
        },*/
        {
          duration: 5*60*1000,
          description: 'warm up - 20% effort'
        },{
          duration: 30*1000,
          description: 'hard - 75% effort'
        },{
          duration: 30*1000,
          description: 'moderate - 60% effort'
        },{
          duration: 30*1000,
          description: 'hard - 75% effort'
        },{
          duration: 30*1000,
          description: 'moderate - 60% effort'
        },{
          duration: 30*1000,
          description: 'hard - 75% effort'
        },{
          duration: 30*1000,
          description: 'moderate - 60% effort'
        },{
          duration: 30*1000,
          description: 'hard - 75% effort'
        },{
          duration: 30*1000,
          description: 'moderate - 60% effort'
        },{
          duration: 1*60*1000,
          description: 'cool down -25% effort'
        },{
          duration: 60 * 1000,
          description: 'hard - 75% effort'
        },{
          duration: 30 * 1000,
          description: 'moderate - 60% effort'
        },{
          duration: 60 * 1000,
          description: 'hard - 75% effort'
        },{
          duration: 30 * 1000,
          description: 'moderate - 60% effort'
        },{
          duration: 60 * 1000,
          description: 'hard - 75% effort'
        },{
          duration: 30 * 1000,
          description: 'moderate - 60% effort'
        },{
          duration: 60 * 1000,
          description: 'hard - 75% effort'
        },{
          duration: 30 * 1000,
          description: 'moderate - 60% effort'
        },{
          duration: 1000,
          description: 'cool down - 25% effort'
        },{
          duration: 45 * 1000,
          description: 'all out - 100% effort'
        },{
          duration: 15*1000,
          description: 'easy - 40% effort'
        },{
          duration: 45 * 1000,
          description: 'all out - 100% effort'
        },{
          duration: 15*1000,
          description: 'easy - 40% effort'
        },{
          duration: 45 * 1000,
          description: 'all out - 100% effort'
        },{
          duration: 15*1000,
          description: 'easy - 40% effort'
        },{
          duration: 2* 60 * 1000,
          description: 'cool down - 25% effort'
        }
      ]
    };
  },
  components: {
    // HelloWorld,
  },
})
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.countdown, .yay{
  font-size: 12rem;
}
.title{
  font-size: 5rem;
}
.stats{
  font-size: 3rem;
  position: absolute;
  bottom:0;
  width:100%;
  right: 0;
  text-align: right;
}
</style>
