<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Blank</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Blank</ion-title>
        </ion-toolbar>
      </ion-header>

      <div id="container">
        <div>Address: {{ testAccountAddress }}</div>
        <div>Balance: {{ addressBalance }}</div>
        <button v-on:click="createEvent">Create Event</button>
        <div>
          Number of events:
          {{ eventCount }}
        </div>
      </div>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {
  IonContent,
  IonHeader,
  IonPage,
  IonTitle,
  IonToolbar,
} from "@ionic/vue";
import { defineComponent, onMounted, onBeforeMount } from "vue";
import { ethers, utils } from "ethers";
import EventFactoryAbi from "../contracts/EventFactoryAbi";

// TODO: Find better way to get rid of the error on window.ethereum
declare var window: any;

export default defineComponent({
  data() {
    const testAccountAddress = "0x70997970C51812dc3A010C7d01b50e0d17dc79C8";
    const addressBalance = "";
    const eventCount = 0;
    const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
    const signer = provider.getSigner();
    const contractAddress = "0xB7f8BC63BbcaD18155201308C8f3540b07f84F5e";
    const eventFactoryContract = new ethers.Contract(
      contractAddress,
      EventFactoryAbi,
      provider
    );
    const eventFactoryWithSigner = eventFactoryContract.connect(signer);
    return {
      testAccountAddress,
      addressBalance,
      eventCount,
      provider,
      signer,
      contractAddress,
      eventFactoryContract,
      eventFactoryWithSigner,
    };
  },
  components: {
    IonContent,
    IonHeader,
    IonPage,
    IonTitle,
    IonToolbar,
  },
  async created() {
    onBeforeMount(async () => {
      var balance = await this.provider.getBalance(this.testAccountAddress);
      this.addressBalance = utils.formatEther(balance) + " eth";
    });
  },
  methods: {
    async createEvent() {
      var createEventTx = await this.eventFactoryWithSigner.createEvent(
        "Name",
        "Location",
        100
      );
      await createEventTx.wait();
      this.eventCount = await this.eventFactoryContract.getNumberOfEvents();
    },
  },
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
</style>
