<template>
  <v-container fluid>
    <div class="controller text-center">
      <v-row>
        <!-- Bên trái -->
        <v-col cols="4">
          <v-row>
            <v-col>
              <v-text-field outlined label="Mã bài hát" v-model="songIdToAdd" clearable></v-text-field>
              <p v-if="msg" class="text--disabled">{{ error }}</p>
              <v-btn @click="addSongToList" :disabled="!songIdToAdd">Thêm</v-btn>
              <v-btn @click="addSongToTop" :disabled="!songIdToAdd">Lên đầu</v-btn>
              <v-btn @click="nextSong">Next</v-btn>
            </v-col>
          </v-row>
        </v-col>

        <!-- Bên phải -->
        <v-col cols="8">
          <div v-if="songList.length" class="border">
            <v-row>
              <v-col cols="3"><v-icon>mdi-play</v-icon> Đang phát</v-col>
              <v-col cols="9">{{ songList[0].name }}</v-col>
            </v-row>
          </div>
          <div v-if="songList.length >= 2" class="mt-4 scroll">
            <v-row>
              <v-col cols="3"><v-icon>mdi-playlist-music</v-icon>Bài tiếp</v-col>
              <v-col cols="7">{{ songList[1].name }}</v-col>
              <v-col cols="2">
                <v-btn elevation="0" fab x-small><v-icon>mdi-arrow-up-bold-circle-outline</v-icon></v-btn>
                <v-btn elevation="0" fab x-small @click="deleteSongFromList(1)"><v-icon>mdi-trash-can-outline</v-icon></v-btn>
              </v-col>
            </v-row>
            <v-row v-for="(item, index) in songList.slice(2,songList.length)" :key="item.index">
              <v-col cols="3"></v-col>
              <v-col cols="7">{{ item.name }}</v-col>
              <v-col cols="2">
                <v-btn elevation="0" fab x-small><v-icon>mdi-arrow-up-bold-circle-outline</v-icon></v-btn>
                <v-btn elevation="0" fab x-small @click="deleteSongFromList(index+2)"><v-icon>mdi-trash-can-outline</v-icon></v-btn>
              </v-col>
            </v-row>
          </div>
        </v-col>
      </v-row>
    </div>

    <div class="adder">
      <v-row>
        <v-col>
          <v-form @submit.prevent="addToDatabase">
            <v-text-field outlined label="Mã bài hát" disabled v-model="songId"></v-text-field>
            <v-text-field outlined label="Tên bài hát" v-model="name"></v-text-field>
            <v-text-field outlined label="Link bài hát" v-model="videoId"></v-text-field>
            <v-btn type="submit">Thêm</v-btn>
            <v-btn @click="count">Đếm</v-btn>
          </v-form>
        </v-col>
      </v-row>
    </div>

    <client-only v-if="songList.length > 0">
      <youtube :video-id="songList[0].videoid" :player-vars="{ autoplay: 1 }" @ready="ready" @ended="end" player-width="1280" player-height="720"></youtube>
    </client-only>
  </v-container>
</template>

<script>
import Vue from 'vue'
import VueYouTubeEmbed from 'vue-youtube-embed'
import { getIdFromURL } from 'vue-youtube-embed'
Vue.use(VueYouTubeEmbed)
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
  data() {
    return {
      songList: [],
      songId: '',
      name: '',
      videoId: '',
      songIdToAdd: '',
      msg: '',
    }
  },
  mounted() {
    this.count()
  },
  methods: {
    async count() {
      this.songId = await this.$axios.$get('/karaokes/count') + 1
    },
    ready (event) {
      this.player = event.target
    },
    end() {
      if (this.songList.length > 0) {
        this.songList.shift()
        this.player.playVideo()
      } else {
        this.player.stopVideo()
      }
    },

    nextSong() {
      this.songList.shift()
    },

    async addToDatabase() {
      try {
        this.$axios.$post('/karaokes', {
          name: this.name,
          songid: this.songId,
          videoid: getIdFromURL(this.videoId)
        })
        this.name = ''
        this.songId = ''
      } catch(e) {

      }
    },
    async addSongToList() {
      try {
        const song = await this.$axios.$get(`/karaokes/${this.songIdToAdd}`)
        this.songIdToAdd = ''
        this.songList.push(song)
      } catch(e) {
        this.msg = 'Đã thêm bài vào list'
        this.songIdToAdd = ''
      }
    },
    async addSongToTop() {
      try {
        const song = await this.$axios.$get(`/karaokes/${this.songIdToAdd}`)
        this.songIdToAdd = ''
        this.songList.splice(1,0,song)
        this.msg = 'Bài thêm bài hát lên đầu!'
      } catch(e) {
        this.msg = 'Bài hát không tồn tại'
        this.songIdToAdd = ''
      }
    },
    deleteSongFromList(index) {
      this.songList.splice(index,1)
    }
  }
}
</script>

<style scoped>
.container {
  height: 100vh;
}
.controller {
  background-color: wheat;
  z-index: 24;
  position: fixed;
  top: 3rem;
  left: 3rem;
  width: 55rem;
  height: 20rem;
  padding: 0.5rem;
}
.border {
  border: 1px black solid;
}
.scroll {
  overflow-x: hidden;
  overflow-y: scroll;
}
.adder {
  background-color: wheat;
  z-index: 24;
  position: fixed;
  top: 3rem;
  right: 3rem;
  width: 30rem;
  height: 15rem;
  padding: 0.5rem;
}
</style>
