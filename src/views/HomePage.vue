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
        <br /><br />
        <p>
          Event Name
          <input type="text" v-model="newEventName" />
        </p>
        <p>
          Event Location
          <input type="text" v-model="newEventLocation" />
        </p>
        <p>
          Event Capacity
          <input v-model="newEventCapacity" number />
        </p>
        <button v-on:click="createEvent">Create Event</button>

        <div>
          Number of events:
          {{ eventCount }}
        </div>
        <ul id="events">
          <li v-for="event in events" :key="event">
            {{ event.name }}
            {{ event.location }}
          </li>
        </ul>
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
import { defineComponent, onBeforeMount } from "vue";
import { ethers, utils } from "ethers";
import EventFactoryAbi from "../contracts/EventFactoryAbi";

// TODO: Find better way to get rid of the error on window.ethereum
declare var window: any;

export default defineComponent({
  data() {
    const testAccountAddress = "0x70997970C51812dc3A010C7d01b50e0d17dc79C8";
    const addressBalance = "";
    const provider = new ethers.providers.Web3Provider(window.ethereum, "any");
    const signer = provider.getSigner();
    const contractAddress = "0x9A9f2CCfdE556A7E9Ff0848998Aa4a0CFD8863AE";
    const eventFactoryContract = new ethers.Contract(
      contractAddress,
      EventFactoryAbi,
      provider
    );
    const eventFactoryWithSigner = eventFactoryContract.connect(signer);
    return {
      testAccountAddress,
      addressBalance,
      eventCount: Number,
      events: [],
      provider,
      signer,
      contractAddress,
      eventFactoryContract,
      eventFactoryWithSigner,
      newEventName: "",
      newEventLocation: "",
      newEventCapacity: 50,
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
        this.newEventName,
        this.newEventLocation,
        this.newEventCapacity
      );
      await createEventTx.wait();
      this.eventCount = await this.eventFactoryContract.getNumberOfEvents();
      this.events = await this.eventFactoryContract.getEvents();
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
