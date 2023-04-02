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
      :key="gif.url"
      width="200"
      height="200"
      :src="gif.url"
      @click="shareGif"
    >
      <i class="share" :data-title="gif.title" :data-url="gif.url"></i>
    </v-img>
  </div>
  <div class="text-center">
    <v-pagination
      class="my-6"
      v-model="currentPage"
      :length="numberOfPages"
      rounded="circle"
    ></v-pagination>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref, watch } from 'vue';
import { logo, API_KEY, IGif, searchEndPoint } from '../variables.js';

//const endPoint: string = 'https://api.giphy.com/v1/gifs/search';

let gifs: any = ref([{ title: 'gif title ', url: 'gif url' }]);
const inputValue = ref('');
var timeoutId: any;
const currentPage: any = ref(1);
const numberOfPages: any = ref(1);

onMounted(() => {
  getGifs('random');
});

watch(inputValue, (newVal: string) => {
  clearTimeout(timeoutId);
  timeoutId = setTimeout(() => {
    currentPage.value = 1;
    getGifs(newVal);
  }, 1000);
});

watch(gifs, (newVal: IGif[]) => {
  if (!newVal.length) {
    getGifs('not found', 1);
  }
});

watch(currentPage, (newVal: number) => {
  const offset: number = (newVal - 1) * 5;
  getGifs(inputValue.value, 5, offset);
});

function shareGif(ev: any) {
  const eventTargChild = ev.target.querySelector('.share');
  navigator.share({
    title: eventTargChild.dataset.title,
    text: eventTargChild.dataset.title,
    url: eventTargChild.dataset.url,
  });
}

function getGifs(q: string, limit: number = 5, offset: number = 0) {
  fetch(
    `${searchEndPoint}?api_key=${API_KEY}&q=${q}&limit=${limit}&offset=${offset}`
  )
    .then((response) => {
      return response.json();
    })
    .then((response) => {
      const gifUrls: IGif[] = response.data.map((el: any) => ({
        title: el.title,
        url: el.images.original.url,
      }));
      gifs.value = gifUrls;
      const amount = Math.floor(response.pagination.total_count / 5);
      const remainder = response.pagination.total_count / 5;
      if (q === 'random') return;
      if (q === 'not found') {
        numberOfPages.value = 1;
        return;
      }
      numberOfPages.value = remainder === 0 ? amount : amount + 1;
    })
    .catch((error) => console.error(error));
}
</script>

<style scoped></style>
