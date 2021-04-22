<template>
  <div class="wrapper">
    <button @click="init">hello</button>
    <table>
      <tr>
        <th>Processed</th>
        <th>Blocked</th>
        <th>Percentage</th>
        <th>Fetched</th>
      </tr>
      <tr v-for="stat in piholeStats" :key="stat">
        <td>{{stat.total}}</td>
        <td>{{stat.blocked}}</td>
        <td>{{test(stat.total, stat.blocked)}}%</td>
        <td>{{stat.fetched}}</td>
      </tr>
    </table>
  </div>

</template>

<script>
import mqtt from 'mqtt';

export default {
  name: 'App',

  props: ['stats'],
  data() {
    return {
      piholeStats: this.stats
    }
  },

  methods: {
    init() {
      this.piholeStats = [];
      const client = mqtt.connect('ws://broker.hivemq.com:8000/mqtt');

      client.on('connect', () => {
        client.subscribe('/testgroupname/stats/pihole');
      });

      client.on('message', (topic, message) => {
        const data = JSON.parse(message.toString());
        data.fetched = new Date();
        console.log(data);
        this.piholeStats.push(data)
      });
    },
    test(total, blocked) {
      if (total !== undefined) {
        total = total.replace(',', '');
        blocked = blocked.replace(',', '');
        const percentage = (Number(blocked) / Number(total) * 100);
        return percentage.toFixed(1)
      }
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
}
* {
  margin : 0;
  padding : 0;
}

table {
  text-align : center;
}

thead {
  font-weight : bold;
  background : forestgreen;
}

tfoot {
  font-weight : bold;
  background : tomato;
}

th, td {
  width : 5vw;
}

.wrapper {
  min-height : 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
