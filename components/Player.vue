<template>
    <v-container>
        <no-ssr>
            <youtube :video-id="video[0]" :player-vars="{ autoplay: 1 }" @ready="ready" @end="end" player-width="1280" player-height="720"></youtube>
        </no-ssr>
    </v-container>
</template>

<script>
import Vue from 'vue'
import VueYouTubeEmbed from 'vue-youtube-embed'
import { getIdFromURL, getTimeFromURL } from 'vue-youtube-embed'
Vue.use(VueYouTubeEmbed)
export default {
    data() {
        return {
            video: ['Zzn9-ATB9aU','fTXd-DpN3AI'],
        }
    },
    methods: {
        ready (event) {
            this.player = event.target
        },
        end(event) {
            this.video.shift()
        }
    }
}
</script>

<style>

</style>