<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js App"/>
  <button @click="init">hello</button>
  <h2>{{piholeStatsElasticsearch}}</h2>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import mqtt from 'mqtt';

export default {
  name: 'App',

  props: ['stats'],
  data() {
    return {
      piholeStats: this.stats
    }
  },

  components: {
    HelloWorld
  },
  methods: {
    init() {
      this.piholeStats = [{}];
      const client = mqtt.connect('ws://broker.hivemq.com:8000/mqtt');

      client.on('connect', () => {
        client.subscribe('/testgroupname/stats/pihole');
      });

      client.on('message', (topic, message) => {
        const data = JSON.parse(message.toString());
        console.log(data);
        this.piholeStats.push(data)
      });
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
