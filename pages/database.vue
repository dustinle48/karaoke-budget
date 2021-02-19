<template>
    <v-container class="text-center">
        <v-row class="tall">
            <v-col class="hidden-md-and-down"></v-col>
            <v-col>
                <v-tabs class="tall" centered>
                    <v-tab>Dò bài</v-tab>
                    <v-tab>Thêm bài</v-tab>
                    <v-tab to="/">Home</v-tab>

                    <v-tab-item class="songlist">
                        <SongList />
                    </v-tab-item>

                    <v-tab-item class="tall">
                        <SongAdd />
                    </v-tab-item>
                </v-tabs>
            </v-col>
            <v-col class="hidden-md-and-down"></v-col>
        </v-row>
    </v-container>
</template>

<script>
export default {
    layout: 'scroll',
}
</script>

<style scoped>
.container {
    min-height: 100vh;
}
.tall {
    height: 100vh;
}
</style>