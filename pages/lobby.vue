<template>
    <v-container fluid class="text-center">
        <v-row justify="center" align="center">
            <v-col class="col-lg-2 hidden-md-and-down"></v-col>
            <v-col>  
                <v-row justify="center">
                    <v-dialog
                        v-model="dialog"
                        persistent
                        width="30rem"
                    >
                        <template v-slot:activator="{ on, attrs }">
                            <v-btn
                                rounded
                                class="big-btn"
                                v-bind="attrs"
                                v-on="on"
                            >
                                Create new room
                            </v-btn>
                        </template>

                        <v-sheet>
                            <validation-observer
                                ref="observer"
                                v-slot="{ invalid }"
                            >
                            <v-form class="pa-6">
                                <h2 class="text-center">Create room</h2>
                                <v-row no-gutters>
                                    <v-col>
                                        <validation-provider
                                            v-slot="{ errors }"
                                            name="Name"
                                            rules="required"
                                        >
                                        <v-text-field
                                            label="Name"
                                            :error-messages="errors"
                                            v-model="room.name"
                                        >
                                        </v-text-field>
                                        </validation-provider>
                                    </v-col>
                                </v-row>
                                <v-row no-gutters>
                                    <v-col>
                                        <v-text-field
                                            label="Password"
                                            v-model="room.password"
                                            disabled
                                        >
                                        </v-text-field>
                                    </v-col>
                                </v-row>

                                <v-row no-gutters class="justify-center align-center text-center">
                                    <v-col>
                                        <v-btn
                                            color="blue darken-1"
                                            text
                                            @click="createRoom"
                                            :disabled="invalid"
                                        >
                                            Create
                                        </v-btn>
                                    </v-col>
                                    <v-col>
                                        <v-btn
                                            color="blue darken-1"
                                            text
                                            @click="dialog = false"
                                        >
                                            Cancel
                                        </v-btn>
                                    </v-col>
                                </v-row>
                            </v-form>
                            </validation-observer>
                        </v-sheet>
                    </v-dialog>
                </v-row>   
            </v-col>
            <v-col class="col-lg-2 hidden-md-and-down"></v-col>
        </v-row>
    </v-container>
</template>

<script>
import swal from 'sweetalert'
import { required } from 'vee-validate/dist/rules'
import { extend, ValidationObserver, ValidationProvider, setInteractionMode } from 'vee-validate'

setInteractionMode('eager')

extend('required', {
...required,
message: '{_field_} can not be empty',
})

export default {
    components: {
        ValidationProvider,
        ValidationObserver,
    },
    data() {
        return {
            dialog: false,

            status: 'success',

            room: {
                name: '',
                password: ''
            },
        }
    },
    mounted() {
    },
    methods: {
        async createRoom() {
            try {
                await this.$axios.$post('/rooms', this.room)
                this.$router.push(`/room/${this.room.name}`)
            } catch(e) {
                if (e?.response?.status == 409) {
                    swal({
                        title: "Duplicate name",
                        text: "This name has been used, try another name!",
                        icon: "error",
                    });
                    return
                }
                else {
                    swal({
                        title: "Oh no!",
                        text: "Error has occured!",
                        icon: "error",
                    });
                    console.log(e)
                }
            } 
        }
    }
}
</script>

<style scoped>
.container {
    min-height: 100vh;
}
.row {
    height: 100%;
}
.big-btn {
    background-color: rgba(53,00,211,0.5) !important;
    height: 10rem !important;
    width: 50%;
    color: white;
    font-size: 2rem;
    backdrop-filter: blur(5px);
    border-top: 1px solid rgba(255,255,255,0.5);
    border-left: 1px solid rgba(255,255,255,0.5);
}
</style>