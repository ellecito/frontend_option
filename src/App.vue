<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-row justify="center">
          <youtube :video-id="videoId" :player-vars="playerVars" v-if="video_selected"></youtube>
        </v-row>
      </v-container>

      <v-form ref="form" v-model="valid" lazy-validation>
        <v-text-field
          label="Another input"
          v-model="v"
          :rules="nameRules"
        ></v-text-field>
        <v-btn
          :disabled="!valid"
          color="success"
          class="mr-4"
          @click="validate"
        >
          Buscar Video
        </v-btn>
      </v-form>
      <v-list two-line>
        <v-list-item-group
          v-model="selected"
          active-class="pink--text"
          multiple
        >
          <template v-for="(item, index) in items">
            <v-list-item :key="item.title" @click="changeVideo(item.id)">
              <template v-slot:default="{ active }">
                <v-list-item-content>
                  <v-list-item-title v-text="item.title"></v-list-item-title>

                  <v-list-item-subtitle
                    class="text--primary"
                    v-text="item.channelTitle"
                  ></v-list-item-subtitle>

                  <v-list-item-subtitle
                    v-text="item.description"
                  ></v-list-item-subtitle>
                </v-list-item-content>

                <v-list-item-action>
                  <v-list-item-action-text
                    v-text="item.link"
                  ></v-list-item-action-text>

                  <v-icon v-if="!active" color="grey lighten-1">
                    mdi-star-outline
                  </v-icon>

                  <!-- <v-icon v-else color="yellow darken-3"> mdi-star </v-icon> -->
                </v-list-item-action>
                <v-list-item-avatar>
                  <v-img :src="item.thumbnails.default.url"></v-img>
                </v-list-item-avatar>
              </template>
            </v-list-item>

            <v-divider v-if="index < items.length - 1" :key="index"></v-divider>
          </template>
        </v-list-item-group>
      </v-list>
    </v-main>
  </v-app>
</template>

<script>
const axios = require("axios");
const prod_or_local = 1;
const server_url = prod_or_local ? 'https://backend-option.herokuapp.com/': 'http://localhost:3000/';

export default {
  name: "App",

  components: {
    // HelloWorld,
  },

  data: () => ({
    valid: true,
    v: "",
    nameRules: [(v) => !!v || "Nombre es requerido"],
    videoId: "lG0Ys-2d4MA",
    playerVars: {
      autoplay: 0,
    },
    selected: [2],
    video_selected: false,
    items: [],
  }),
  methods: {
    validate() {
      if (this.$refs.form.validate()) {
        this.search();
      }
    },
    search() {
      axios
        .get(server_url + "list/" + this.v)
        .then((response) => {
          this.items = response.data;
          console.log(response.data);
        })
        .catch((error) => console.error(error));
    },
    changeVideo(id) {
      this.video_selected = true;
      this.videoId = id;
      this.playerVars.autoplay = 1;
    },
  },
};
</script>
