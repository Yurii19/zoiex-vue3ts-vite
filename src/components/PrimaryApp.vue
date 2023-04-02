<template>
  <v-container>
    <v-row no-gutters>
      <v-col cols="3">
        <v-sheet class="pa-2">
          <v-img class="" width="100" :src="logo"></v-img>
        </v-sheet>
      </v-col>
      <v-col cols="6">
        <v-sheet class="pa-2">
          <v-text-field
            v-model="inputValue"
            hide-details
            prepend-icon="mdi-magnify"
            single-line
          ></v-text-field>
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
  <div class="d-flex">
    <v-img
      v-for="gif in gifs"
      :key="gif"
      width="200"
      height="200"
      :src="gif"
    ></v-img>
  </div>
  <p>
    <a href="https://giphy.com/gifs">via GIPHY</a>
  </p>
  <div class="text-center">
    <v-pagination
      v-model="currentPage"
      :length="numberOfPages"
      rounded="circle"
    ></v-pagination>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref, watch } from 'vue';
import { logo, API_KEY } from '../variables.js';

const endPoint: string = 'https://api.giphy.com/v1/gifs/search';

const gifs = ref(['']);
const inputValue = ref('');
var timeoutId: any;
const currentPage: any = ref(1);
const numberOfPages: any = ref(2);

watch(inputValue, (newVal) => {
  clearTimeout(timeoutId);
  timeoutId = setTimeout(() => {
    getGifs(newVal);
  }, 1000);
});

watch(gifs, (newVal) => {
  if (!newVal.length) {
    getGifs('not found', 1);
  }
});

watch(currentPage, (newVal) => {
  console.log('current page is: ', newVal);
});

onMounted(() => {
  getGifs('random');
});

function getGifs(q: string, limit: number = 5, offset: number = 0) {
  fetch(`${endPoint}?api_key=${API_KEY}&q=${q}&limit=${limit}&offset=${offset}`)
    .then((response) => {
      // console.log(d);
      return response.json();
    })
    .then((response) => {
      console.log(response);
      const gifUrls = response.data.map((el: any) => el.images.original.url);
      gifs.value = gifUrls;
      const amount = Math.floor(response.pagination.total_count / 5);
      const remainder = response.pagination.total_count / 5;
      //numberOfPages.value = response.pagination.total_count / ;
      if (remainder === 0) {
        numberOfPages.value = amount;
      } else {
        numberOfPages.value = amount + 1;
      }
    })
    .catch((error) => console.error(error));
}
</script>

<style scoped></style>
