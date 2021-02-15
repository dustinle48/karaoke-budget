<template>
    <v-container fluid>
        <v-menu
            class="menu"
            :close-on-content-click="false"
            offset-x
        >
        <template v-slot:activator="{ on, attrs }">
            <v-btn
            rounded
            v-bind="attrs"
            v-on="on"
            class="menu-btn"
            >
                <v-icon>mdi-post</v-icon>
            </v-btn>
        </template>

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
                        <v-btn to="/"><v-icon>mdi-home</v-icon></v-btn>
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
        </v-menu>

        <div class="text-center">
            <h1 v-if="songList.length == 0">Chọn bài đi, đừng ngại</h1>
        </div>

        <client-only>
            <div class="youtube" v-if="songList.length > 0">
                <youtube :video-id="songList[0].videoid" :player-vars="{ autoplay: 1 }" @ready="ready" @ended="end" player-width="1920" player-height="1080"></youtube>
            </div>
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
        title: 'Karaoke',
        meta: [
        {
            hid: 'Karaoke',
            name: 'Karaoke',
            content: 'Karaoke'
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
    methods: {
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
    overflow: hidden;
    padding: 0;
}
.menu-btn {
    position: fixed;
    top: 2rem;
    left: 2rem;
}
.controller {
    background-color: wheat;
    z-index: 24;
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
.youtube {
}
</style>
