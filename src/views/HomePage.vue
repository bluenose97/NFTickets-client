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

      <div>
        {{ addressInfo }}
      </div>
      <div>
        {{ addressBalance }}
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
import { defineComponent, onMounted } from "vue";
import { ethers, utils } from "ethers";

// TODO: Find better way to get rid of the error on window.ethereum
declare var window: any;

export default defineComponent({
  data() {
    const addressInfo = "";
    const addressBalance = "";
    return {
      addressInfo,
      addressBalance,
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
    // A Web3Provider wraps a standard Web3 provider, which is
    // what MetaMask injects as window.ethereum into each page
    const provider = new ethers.providers.Web3Provider(window.ethereum);

    // The MetaMask plugin also allows signing transactions to
    // send ether and pay to change state within the blockchain.
    // For this, you need the account signer...
    const signer = provider.getSigner();

    onMounted(async () => {
      // Get the balance of an account (by address or ENS name, if supported by network)
      var balance = await provider.getBalance(
        "0x70997970C51812dc3A010C7d01b50e0d17dc79C8"
      );
      this.addressInfo = "Address: 0x70997970C51812dc3A010C7d01b50e0d17dc79C8";
      this.addressBalance = utils.formatEther(balance) + " eth";
    });
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
