<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>i</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">i</ion-title>
        </ion-toolbar>
      </ion-header>
    
      <div id="container">
        <span style="color: #FF0000" v-if="error">
          {{ error }}
        </span>
        <span v-if="device.deviceId !== ''">Connecté à {{ device.deviceId }}</span>
        <span>Incrémentation : {{ i }}</span>
        <ion-button @click="refresh()" expand="block">Rafraîchir</ion-button>
        <span v-if="false">
          Connecté à {{ device.deviceId }} (RSSI : {{ rssi }})
        </span>

        <Check v-if="rssi"/>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonButton } from '@ionic/vue';
import { defineComponent } from 'vue';

import { BleClient, BleDevice } from '@capacitor-community/bluetooth-le';

import Check from './Check.vue';

export default defineComponent({
  components: {
    IonContent,
    IonHeader,
    IonButton,
    IonPage,
    IonTitle,
    IonToolbar,
    Check
  },

  data() {
    return {
      status: '',
      rssi: 0,
      device: {
        deviceId: ''
      },
      error: '',
      i: 0
    };
  },

  methods: {
    async refresh() {
      try {
        const device = this.device;
        await BleClient.connect(device.deviceId);
        const rssi = await BleClient.readRssi(device.deviceId);

        this.rssi = rssi;
      } catch (e) {
        this.error = `${e}`;
      }
    }
  },

  async mounted() {
    try {
      await BleClient.initialize();

      const enabled = await BleClient.isEnabled();

      if (!enabled) {
        await BleClient.enable();
      }

      console.log('NICO : début du scan');

      // 0000fd5a-0000-1000-8000-00805f9b34fb
      // d0611e78-bbb4-4591-a5f8-487910ae436
      await BleClient.requestLEScan({
        services: ['0000fd5a-0000-1000-8000-00805f9b34fb']
      }, async (result) => {
        await BleClient.connect(result.device.deviceId);
        this.device = result.device;

        setInterval(() => {
          BleClient.readRssi(result.device.deviceId).then(rssi => {
            if (rssi > -60) {
              this.rssi = rssi;

              this.i++;
            }
          });
        }, 1000);
      });

    } catch (e) {
      this.error = `${e}`;
    }
  }
});
</script>

<style scoped>
#container {
  text-align: center;
  
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  
  color: #8c8c8c;
  
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>