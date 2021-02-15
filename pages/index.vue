<template>
  <v-container fluid class="text-center">
    <v-row class="component" justify="center" align="center">
      <v-col class="col-lg-2 hidden-md-and-down"></v-col>
      <v-col>
        <h1 class="mb-12">Chào mừng tới Budget Karaoke</h1>
        <v-btn to="/karaoke" color="red" x-large>Quẩy</v-btn>
        <v-btn to="/database" color="green" x-large>Database</v-btn>
      </v-col>
      <v-col class="col-lg-2 hidden-md-and-down"></v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  head: {
    title: 'Home',
    meta: [
      {
        hid: 'Home',
        name: 'Home',
        content: 'Home'
      }
    ],
  },
}
</script>

<style scoped>
.container {
  height: 100vh;
  align-content: center;
  background-image: url(/bg.JPG);
  background-size: cover;
}
.component {
  min-height: 100vh;
}
.wrapper {
  margin-top: auto;
  margin-bottom: auto;
}
.v-btn {
  margin: 0.5rem;
}
h1 {
  font-weight: 800;
  font-size: 5rem;
  color: white;
  -webkit-text-stroke: 0.1rem black;
}
</style>
