<template>
    <v-app id="inspire">
      <v-navigation-drawer v-model="drawer">
        <template #prepend>
          <div class="d-flex align-center" style="height:64px;">
            <v-avatar
              image="https://cdn.vuetifyjs.com/docs/images/brand-kit/v-logo.svg"
              rounded="0"
              size="48"
              class="ms-3 me-3"
            >
            </v-avatar>
            <v-toolbar-title>
              APP NAME
            </v-toolbar-title>
          </div>
        </template>
  
        <v-divider></v-divider>
  
        <v-list nav>
          <v-list-subheader>HOME</v-list-subheader>
          <v-list-item prepend-icon="mdi-view-dashboard" title="Analytical" value="Analytical">
          </v-list-item>
        </v-list>
      </v-navigation-drawer>
      <v-app-bar>
        <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
  
        <!-- <v-app-bar-title>Dashboard</v-app-bar-title> -->
        <template #append>
          <v-btn icon="mdi-dots-vertical"></v-btn>
        </template>
      </v-app-bar>
  
      <v-main>
        <v-container class="pb-sm-15 pb-10" fluid>
          <div class="mt-3 mb-6 ">
            <div class="d-flex justify-space-between">
              <div>
                <h3 class="text-h3 mb-2">
                  Analytical
                </h3>
                <v-breadcrumbs
                  :items="breadcrumbsItems"
                  color="text-h6 font-weight-regular pa-0 ml-n1"
                >
                  <template #divider>
                    <v-icon icon="mdi-chevron-right"></v-icon>
                  </template>
                </v-breadcrumbs>
              </div>
            </div>
          </div>
  
          <v-row>
            <v-col>
              <v-card class="rounded-lg">
                <v-card-title>Temperature</v-card-title>
                <!-- <v-card-subtitle>This is a subtitle</v-card-subtitle> -->
                <v-card-text>
                  <div class="text-center">
                    <v-progress-circular
                      :rotate="0"
                      :size="125"
                      :width="15"
                      :model-value="DHT.temp"
                      color="primary"
                    >
                      {{ DHT.temp.toFixed(2) }} Â°C
                    </v-progress-circular>
                  </div>
                </v-card-text>
              </v-card>
            </v-col>
            <v-col>
              <v-card class="rounded-lg">
                <v-card-title>Humidity</v-card-title>
                <!-- <v-card-subtitle>This is a subtitle</v-card-subtitle> -->
                <v-card-text>
                  <div class="text-center">
                    <v-progress-circular
                      :rotate="0"
                      :size="125"
                      :width="15"
                      :model-value="DHT.humi"
                      color="primary"
                    >
                      {{ DHT.humi.toFixed(2) }} %
                    </v-progress-circular>
                  </div>
                </v-card-text>
              </v-card>
            </v-col>
            <v-col>
              <v-card class="rounded-lg">
                <v-card-title>Light Bulb Led</v-card-title>
                <!-- <v-card-subtitle>This is a subtitle</v-card-subtitle> -->
                <v-card-text>
                  <div class="text-center">
                    <v-btn
                      size="125"
                      variant="tonal"
                      :color="light.color"
                      class="rounded-circle"
                      @click="toggleLight"
                    >
                      <v-icon
                        size="60"
                        :icon="light.icon"
                      ></v-icon>
                    </v-btn>
                  </div>
                  <v-card-actions>
                    <v-switch
                      v-model="light.isOn"
                      hide-details
                      @click="toggleLight"
                    ></v-switch>
                    <v-spacer></v-spacer>
                    {{ light.isOn ? 'ON' : 'OFF' }}
                  </v-card-actions>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
          <v-row>
            <v-col
              sm="6"
              lg="3"
              class="py-0 mb-3"
            >
              <v-card
                class="rounded-lg"
              >
                <v-card-text>
                  <div class="d-flex align-center">
                    <v-btn
                      size="large"
                      variant="tonal"
                      color="deep-purple-darken-4"
                      icon="mdi-tape-measure"
                    >
                    </v-btn>
                    <div>
                      <v-card-subtitle>
                        Distance
                      </v-card-subtitle>
                      <v-card-title class="py-0">
                        {{ distance.toFixed(2) }} cm
                      </v-card-title>
                    </div>
                  </div>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-container>
      </v-main>
    </v-app>
  </template>
  
  <script>

  
  export default {
    setup() {
    useHead({
      script: ["/mqtt.min.js"],
    });
  },
    data: () => ({
      drawer: null,
      breadcrumbsItems: [
        {
          title: 'Dashboard',
          disabled: false,
        },
        {
          title: 'Analytical ',
          disabled: true,
          href: '#',
        },
      ],
      DHT: {
        temp: 0,
        humi: 0,
      },
      light: {
        isOn: false,
        icon: 'mdi-lightbulb-outline',
        color: 'grey',
      },
      distance: 0,
    }),
    beforeMount() {
      this.mqttTask()
    },
    methods: {
      mqttTask() {
        this.client = mqtt.connect('ws://broker.emqx.io:8083/mqtt')
        this.client.on('connect', () => {
          console.log('on client connect')
          this.client.subscribe('joseph/DHT')
          this.client.subscribe('joseph/light')
          this.client.subscribe('joseph/distance')
        })
        this.client.on('message', (topic, message) => {
          switch (topic) {
            case 'kp/DHT': {
              const DHT = message.toString()
              const [temp, humi] = DHT.split(',')
              this.DHT = {
                temp: +temp,
                humi: +humi,
              }
              break
            }
            case 'kp/light': {
              const light = message.toString()
              this.light.isOn = !!+light
              if (this.light.isOn) {
                this.light.icon = 'mdi-lightbulb-on-outline'
                this.light.color = 'amber-darken-4'
              } else {
                this.light.icon = 'mdi-lightbulb-off-outline'
                this.light.color = 'grey-darken-4'
              }
              break
            }
            case 'kp/distance': {
              const distance = message.toString()
              this.distance = +distance
              break
            }
          }
        })
      },
      toggleLight() {
        console.log('toggleLight')
        this.light.isOn = !this.light.isOn
        this.client.publish('kp/light/set', +this.light.isOn + '')
      },
    },
  }
  </script>
  
  <style lang="scss">
  main {
    background-color: #FAFAFA;
  }
  </style>