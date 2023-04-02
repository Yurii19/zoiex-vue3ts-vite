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
</template>

<script lang="ts" setup>
import { onMounted, ref, watch } from 'vue';

const API_KEY = 'FP6iXCRdSYnQlrHGySqUcQloW820c08i';
const logo: string =
  'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ_T835X5Z6ADme_fYEWOO03j7DfEjJoRjVwUtvMo8t5w&s';
const endPoint: string = 'https://api.giphy.com/v1/gifs/search';

const gifs = ref([
  'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ_T835X5Z6ADme_fYEWOO03j7DfEjJoRjVwUtvMo8t5w&s',
]);
const inputValue = ref('');
var timeoutId: any;

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

onMounted(() => {
  getGifs('random');
});

function getGifs(q: string, limit: number = 5) {
  fetch(`${endPoint}?api_key=${API_KEY}&q=${q}&limit=${limit}`)
    .then((d) => {
      // console.log(d);
      return d.json();
    })
    .then((response) => {
      console.log(response);
      const gifUrls = response.data.map((el: any) => el.images.original.url);
      gifs.value = gifUrls;
    })
    .catch((error) => console.error(error));
}

</script>

<style scoped></style>
