<template>
  <v-container>
    <v-row align="center" justify="center">
      <!-- <v-card-subtitle>This is a subtitle</v-card-subtitle> -->
      <v-col cols="auto">
        <v-btn color="primary" @click="bntSensor1">เปิดไฟ</v-btn>
      </v-col>

      <v-col cols="auto">
        <v-btn color="success" @click="bntSensor2">ปิดไฟ</v-btn>
      </v-col>
    </v-row>
    <v-spacer> </v-spacer>
    <v-spacer></v-spacer>


    <div class="text-center">
      <v-btn size="120" variant="tonal" :color="lightColor" class="rounded-circle">
        <v-icon size="60" :icon="isLight2On"></v-icon>
      </v-btn>
    </div>

    <div class="text-center">

      <v-progress-circular :rotate="-90" :size="100" :width="15" :model-value="value" color="primary">
        {{ value }}
        ℃
      </v-progress-circular>

      <v-progress-circular :rotate="90" :size="100" :width="15" :model-value="value" color="red">
        {{ value2 }}
        %
      </v-progress-circular>

      <v-progress-circular :rotate="90" :size="100" :width="15" :model-value="value" color="red">
        {{ value3 }}
        cm
      </v-progress-circular>
    </div>
  </v-container>
</template>
<script>
//var mqtt = require("mqtt");
// import * as mqtt from "dist/mqtt/mqtt.min";
// Rest of your file goes h
//import * as mqtt from "mqtt";
export default {
  setup() {
    useHead({
      script: ["/mqtt.min.js"],
    });
  },
  data() {
    return {
      value: 29,
      value2: 30,
      value3: 31,
      isLight2On: "mdi-lightbulb-off-outline",
      lightColor: "grey-darken-4",
      sensor1Value: "0",
      sensor2Value: "0",
    };
  },
  beforeMount() {
    this.client = mqtt.connect("ws://broker.emqx.io:8083/mqtt");
    this.client.on("connect", () => {
      console.log("on client connect");
      this.client.subscribe("kp/light");
      this.client.subscribe('kp/DHT')
      this.client.subscribe('kp/distance')
    });
    this.client.on("message", (topic, message) => {
      if (topic === "kp/light") {
        const light = message.toString()
        if (!!+light) {
          this.isLight2On = 'mdi-lightbulb-on-outline'
          this.lightColor = 'amber-darken-4'
        } else {
          this.isLight2On = 'mdi-lightbulb-off-outline'
          this.lightColor = 'grey-darken-4'
        }
      }
      if (topic === "kp/DHT") {
        const DHT = message.toString()
        const [humi, temp] = DHT.split(',')
        this.value = +temp
        this.value2 = +humi

      }
      if (topic === "kp/distance") {
        const distance = message.toString()
        this.value3 = +distance
      }
    });
  },
  methods: {
    bntSensor1() {
      this.client.publish("kp/light/set", '1');
    },
    bntSensor2() {
      this.client.publish("kp/light/set", '0');
    },
  },
};
</script>