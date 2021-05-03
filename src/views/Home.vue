<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>Inbox</ion-title>
      </ion-toolbar>
    </ion-header>
    
    <ion-content :fullscreen="true">
      <ion-refresher slot="fixed" @ionRefresh="refresh($event)">
        <ion-refresher-content></ion-refresher-content>
      </ion-refresher>
      
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">Inbox</ion-title>
        </ion-toolbar>
      </ion-header>
      <!-- Lista de mensajes -->
      <ion-list>
        <MessageListItem v-for="message in messages" :key="message.id" :message="message" />
      </ion-list>

      <!-- Scroll de productos -->
      <ion-list>
        <ion-item v-for="product in products" :key="product.id">
          <ion-label>
            {{product.name}}
          </ion-label>
        </ion-item>
      </ion-list>
      <ion-infinite-scroll
        @ionInfinite="getProducts()">
      </ion-infinite-scroll>
    </ion-content>
  </ion-page>
</template>

<script lang="ts">
import {
  IonContent,
  IonHeader,
  IonList,
  IonPage,
  IonRefresher,
  IonRefresherContent,
  IonTitle,
  IonToolbar,
  IonInfiniteScroll,
  IonLabel,
  IonItem
} from '@ionic/vue';
import MessageListItem from '@/components/MessageListItem.vue';
import { defineComponent, ref } from 'vue';
import { getMessages } from '@/data/messages';
import axios from 'axios';

export default defineComponent({
  name: 'Home',
  setup() {
    const messages = getMessages();
    const refresh = (ev: CustomEvent) => {
      setTimeout(() => {
        ev.detail.complete();
      }, 3000);
    }

    const products = ref([]);
    const getProducts = () => {
      axios.get("https://localhost:5001/api/products").then(r => {
        products.value = r.data
      })
      return []
    }

    return {
      messages,
      refresh,
      products,
      getProducts,
    }
  },
  components: {
    IonContent,
    IonHeader,
    IonList,
    IonPage,
    IonRefresher,
    IonRefresherContent,
    IonTitle,
    IonToolbar,
    MessageListItem,
    IonInfiniteScroll,
    IonLabel,
    IonItem
  }
})
</script>