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
      <!-- <v-icon
        class="share"
        icon="mdi-share"
        :data-title="gif.title"
        :data-url="gif.url"
        @click="shareGif"
      ></v-icon
    > -->
      <i class="share" :data-title="gif.title" :data-url="gif.url"></i>
    </v-img>
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
import { logo, API_KEY, IGif } from '../variables.js';

const endPoint: string = 'https://api.giphy.com/v1/gifs/search';

let gifs: any = ref([{ title: 'gif title ', url: 'gif url' }]);
const inputValue = ref('');
var timeoutId: any;
const currentPage: any = ref(1);
const numberOfPages: any = ref(1);
//let numberOfGifs: number = 0;

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
    // setTimeout(() => {
    //   numberOfPages.value = 1;
    // }, 1);
  }
});

watch(currentPage, (newVal: number) => {
  if (newVal === 1) return;
  const offset: number = (newVal - 1) * 5;
  getGifs(inputValue.value, 5, offset);
  console.log('current page is: ', newVal);
});

onMounted(() => {
  getGifs('random');
});

function shareGif(ev: any) {
  const eventTargChild = ev.target.querySelector('.share');
  console.log('evtTargChild ', eventTargChild);
  navigator.share({
    title: 'Gif',
    text: eventTargChild.dataset.title,
    url: eventTargChild.dataset.url,
  });
  //console.log('share', ev.target);
}

function getGifs(q: string, limit: number = 5, offset: number = 0) {
  //currentPage.value = 1;
  fetch(`${endPoint}?api_key=${API_KEY}&q=${q}&limit=${limit}&offset=${offset}`)
    .then((response) => {
      return response.json();
    })
    .then((response) => {
      console.log(response);
      const gifUrls: IGif[] = response.data.map((el: any) => ({
        title: el.title,
        url: el.images.original.url,
      }));
      gifs.value = gifUrls;
      const amount = Math.floor(response.pagination.total_count / 5);
      const remainder = response.pagination.total_count / 5;
      //numberOfPages.value = response.pagination.total_count / ;
      if (q === 'random') return;
      if (q === 'not found') {
        numberOfPages.value = 1;
        return;
      }
      if (remainder === 0) {
        numberOfPages.value = amount;
      } else {
        numberOfPages.value = amount + 1;
      }
    })
    .catch((error) => console.error(error));
}
</script>

<style scoped>
.share {
  /* background-color: black;
  color: white;
  border-radius: 50%;
  padding: 15px;
  cursor: pointer;
  border: 2px solid white; */
}
</style>
