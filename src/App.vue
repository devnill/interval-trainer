
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import Countdown from './components/Countdown.vue';
import Stats from './components/Stats.vue';
import Debug from './components/Debug.vue';

@Component({
  components: {
    Countdown,
    Stats,
    Debug
  },
  methods:{
    start(){
      if(this.startTime === null){
        this.startTime = Date.now() + (this.config.startDelay*1000);
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
        formattedTime = `${formattedTime}:${ms.toString().substr(0,2).toString().padStart(2, '0')}`
      }
      formattedTime = `${minutes.toString().padStart(2,'0')}:${formattedTime}`
      if(hours){
        formattedTime = `${hours.toString().padStart(2,'0')}:${formattedTime}`
      }
      return formattedTime;
    }
  },
  computed: {
    clockState(){
      if(this.timeElapsed === null){
        return 'IDLE'
      } else if(this.timeElapsed<0){
        return 'PRESTART';
      } else if(this.timeElapsed && this.timeElapsed > this.overallDuration){
        return 'FINISHED'
      } else if(this.currentHeat){
        return 'IN_PROGRESS'
      }

    },
    timeUntilStart(){
      return this.formatTime(Math.abs(this.timeElapsed));
    },
    heats(){
      return this.config.plan.reduce((acc, cur)=>{
        const lastHeat = acc.length ? acc[acc.length - 1] : {
          endTime: 0
        };
        const startTime = lastHeat.endTime;
        const formattedHeat = Object.assign({
          startTime,
          endTime: startTime + cur.duration
        }, cur);
        acc.push(formattedHeat)
        return acc;
      },[]);      
    },
    lastHeat(){
      return this.heats[this.heats.length - 1];
    },
    overallDuration(){
      return this.lastHeat.endTime;
    },
    timeLeftOverall(){
      return this.overallDuration - this.timeElapsed;
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
    currentHeat(){
      return this.heats[this.currentHeatIndex]
    },
    currentHeatIndex(){
      const currentHeatIndex = this.heats.findIndex((heat)=>{
        return this.timeElapsed > heat.startTime && this.timeElapsed <= heat.endTime
      });
      return currentHeatIndex !== -1 ? currentHeatIndex : null;
    },
    timeLeftInHeat(){
      if(!this.currentHeat){
        return null;
      } 
      return  this.currentHeat.endTime - this.timeElapsed
    }
  },
  data(){
    return {
      startTime: null,
      timeElapsed: null,
      config: {
        startDelay: 30,
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
      }
    };
  }
})
export default class App extends Vue {}
</script>

<template>
  <div id="app">
    <div class='countdown' v-if="clockState=='PRESTART'">
      <countdown prefix="Starting in" :time="timeUntilStart" />
    </div>
    <div class='yay' v-else-if="clockState=='FINISHED'">
      YOU DID IT!!!
    </div>
    <div v-else-if="clockState==='IN_PROGRESS'">
      <countdown :time="formattedTimeLeftInHeat" :description="currentHeat.description"/>  
      <stats :timeRemaining="formattedTimeLeftOverall" :timeElapsed="formattedTimeElapsed" />
    </div>
    <div v-if="clockState === 'IDLE' || clockState === 'FINISHED'">
      <button @click="start">start</button>
      <button @click="reset">reset</button>
      <div>Start in <input type="number" v-model="config.startDelay"/> seconds</div>
    </div>
    <div v-else>
      <button @click="reset">reset</button>
    </div>
  </div>
</template>


<style>
#app {
  font-family: sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
