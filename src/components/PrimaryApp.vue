<template>
  <v-container>
    <v-row no-gutters>
      <v-col cols="3">
        <v-sheet class="pa-2">
          <v-img
            class=""
            width="100"
            src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQ_T835X5Z6ADme_fYEWOO03j7DfEjJoRjVwUtvMo8t5w&s"
          ></v-img>
        </v-sheet>
      </v-col>
      <v-col cols="6">
        <v-sheet class="pa-2">
          <v-text-field
            hide-details
            prepend-icon="mdi-magnify"
            single-line
          ></v-text-field>
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
  <iframe
    src="https://giphy.com/embed/dce5V6huS7QwhI3Xn6"
    width="343"
    height="480"
    frameBorder="0"
    class="giphy-embed"
    allowFullScreen
  ></iframe>
  <p>
    <a href="https://giphy.com/gifs">via GIPHY</a>
  </p>
</template>

<script lang="ts" setup>
const name = 'Hello from Primary component';
</script>

<style scoped></style>
