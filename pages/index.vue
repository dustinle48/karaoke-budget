<template>
  <v-container fluid class="text-center">
    <v-row justify="center" align="center">
      <v-col class="col-lg-2 hidden-md-and-down"></v-col>
      <v-col>
        <v-row justify="center" align="center">
          <h1 class="my-5 white--text text-lg-h1 text-md-h2 text-sm-h3 text-h4">Welcome to <strong>SAYC</strong> Karaoke</h1>
        </v-row>

        <v-row>
          <v-col class="pa-6 hidden-xs-only">
            <v-btn to="/lobby" rounded class="big-btn server-btn">Server</v-btn>
          </v-col>
          <v-col class="pa-6">
            <v-btn to="/database" rounded class="big-btn client-btn elevation-10">Client</v-btn>
          </v-col>
        </v-row>

        <v-row justify="center" align="center">
          <v-dialog
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                v-bind="attrs"
                v-on="on"
              >
                Here is tutorial
              </v-btn>
            </template>

            <v-sheet>
              <v-img src="/Karaoke.jpg"></v-img>
            </v-sheet>
          </v-dialog>
        </v-row>
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
        hid: 'description',
        name: 'description',
        content: 'Home'
      }
    ],
  },
}
</script>

<style scoped>
.container {
  min-height: 100vh;
  background: linear-gradient(black,#0C0032)
}
.row {
  height: 100%;
}
.big-btn {
  margin: 0.5rem;
  height: 20rem !important;
  width: 100%;
  box-shadow: 20px rgba(0,0,0,0.5) !important;
  backdrop-filter: blur(50px);
  border-top: 1px solid rgba(255,255,255,0.5);
  border-left: 1px solid rgba(255,255,255,0.5);
}
.server-btn {
  color: white;
  font-size: 2rem;
  background-image: linear-gradient(rgba(40,40,40,0.5),rgba(40,40,40,0.7)), url('/theatre.jpg');
  background-size: cover;
  background-blend-mode: multiply;
}
.client-btn {
  color: white;
  font-size: 2rem;
  background-image: linear-gradient(rgba(53,0,211,0.7),rgba(53,0,211,0.7)), url('/mic.jpg');
  background-size: cover;
  background-blend-mode: multiply;
}
</style>
