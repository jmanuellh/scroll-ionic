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

      <!-- Agregar producto -->
      <ion-item>
        <ion-label position="floating">
          Nombre
        </ion-label>
        <ion-input v-model="product.name" />
      </ion-item>
      <ion-item>
        <ion-label position="floating">
          Precio
        </ion-label>
        <ion-input v-model="product.price" />
      </ion-item>
      <ion-button @click="addProduct()">
        + Agregar
      </ion-button>

      <!-- Scroll de productos -->
      <ion-list>
        <ion-item-group v-for="product in products" :key="product.id">
          <ion-item-divider>
            <ion-label>
              {{product.id}}
            </ion-label>
          </ion-item-divider>
          <ion-item>
            <ion-label>
              <ion-note>
                Nombre
              </ion-note>
            </ion-label>
            <ion-label slot="end">{{product.name}}</ion-label>
          </ion-item>
          <ion-item>
            <ion-label>
              <ion-note>
                Precio
              </ion-note>
            </ion-label>
            <ion-label slot="end">{{product.price}}</ion-label>
          </ion-item>
        </ion-item-group>
      </ion-list>
      <ion-infinite-scroll
        @ionInfinite="getProducts()"
        threshold="100px"
        id="infinite-scroll"
        :disabled="isDisabled">
        <ion-infinite-scroll-content
          loading-spinner="bubbles"
          loading-text="Loading more data..."
        >
        </ion-infinite-scroll-content>
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
  IonItem,
  IonInput,
  IonButton,
  IonItemDivider,
  IonItemGroup,
  IonNote,
  IonInfiniteScrollContent
} from '@ionic/vue';
import MessageListItem from '@/components/MessageListItem.vue';
import { defineComponent, ref, reactive } from 'vue';
import { getMessages } from '@/data/messages';
import axios from 'axios';

export default defineComponent({
  name: 'Home',
  setup() {
    // Mensajes
    const messages = getMessages();
    const refresh = (ev: CustomEvent) => {
      setTimeout(() => {
        ev.detail.complete();
      }, 3000);
    }

    // Productos
    //  Vairables
    const isDisabled = ref(false);
    const product = reactive({
      name: "producto prueba " + Math.floor(Math.random() * 1000),
      price: Math.floor(Math.random() * 1000)
    });
    const products = ref([]);
    //  Metodos
    const clearProduct = () => {
      product.name = "producto prueba " + Math.floor(Math.random() * 1000);
      product.price = Math.floor(Math.random() * 1000);
    }
    const getProducts = async () => {
      isDisabled.value = false;
      axios.get("https://localhost:5001/api/products").then(r => {
        products.value = r.data;
      }).catch(error => {
        console.error(error);
      }).finally(() => {
        isDisabled.value = true;
      })
    }
    const addProduct = async () => {
      axios.post("https://localhost:5001/api/products", product).then(() => {
        getProducts()
      })
      .catch(error => {
        console.log(error)
      })
      .finally(() => {
        clearProduct();
      })
    }

    return {
      messages,
      refresh,
      product,
      products,
      addProduct,
      getProducts,
      isDisabled,
      clearProduct
    }
  },
  created() {
    this.getProducts()
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
    IonItem,
    IonInput,
    IonButton,
    IonItemDivider,
    IonItemGroup,
    IonNote,
    IonInfiniteScrollContent
  }
})
</script>