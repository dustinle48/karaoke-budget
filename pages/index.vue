<template>
  <v-container fluid class="text-center">
    <v-row class="component" justify="center" align="center">
      <v-col class="col-lg-2 hidden-md-and-down"></v-col>
      <v-col>
        <!-- <div class="overlay">
          <h2>
            Hey if this is your first visit, click here for tutorial!
          </h2>
        </div> -->
        <h1 class="mb-12">Welcome to Budget Karaoke</h1>
        <v-btn to="/lobby" color="red" x-large>Server</v-btn>
        <v-btn to="/database" color="green" x-large>Client</v-btn>
        <br>
        <v-btn>If this is your first visit, here is tutorial</v-btn>
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
  background-image: url(/bg.JPG);
  background-size: cover;
}
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  z-index: 9999;
  background: green;
  opacity: 25%;
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
  font-size: 4rem;
  color: white;
  -webkit-text-stroke: 0.1rem black;
}
</style>
