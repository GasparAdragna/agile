<template>
  <div class="home">
    <v-row v-if="!ready">
      <v-col cols="12" justify="center">
        <v-progress-circular
          :size="50"
          color="primary"
          indeterminate
        ></v-progress-circular>
      </v-col>
    </v-row>
    <v-row v-if="ready">
      <v-col v-for="photo in photos" :key="photo.id" cols="12" md="4">
        <a @click="photoView(photo.id)">
          <v-img :src="photo.cropped_picture"></v-img
        ></a>
      </v-col>
    </v-row>

    <v-dialog v-model="dialog">
      <v-carousel v-model="stepNum" height="100%">
        <v-carousel-item
          v-for="(item, i) in photos"
          :key="i"
          reverse-transition="fade-transition"
          transition="fade-transition"
          justify="center"
          class="text-center"
        >
          <img :src="photo.full_picture" style="height:auto;" />
          <v-overlay absolute class="d-flex align-end" opacity="0">
            <p><b>Author: </b>{{ photo.author }}</p>
            <p><b>Camera: </b>{{ photo.camera }}</p>
            <p><b>Tags: </b>{{ photo.tags }}</p>
            <br /><br />
          </v-overlay>
        </v-carousel-item>
      </v-carousel>
    </v-dialog>
  </div>
</template>

<script>
const axios = require("axios");

export default {
  name: "Home",
  data() {
    return {
      token: null,
      photos: [],
      photo: {},
      page: 0,
      dialog: false,
      ready: false,
      stepNum: 0
    };
  },
  created: async function() {
    if (this.token == null) {
      try {
        const response = await axios.post(
          "http://interview.agileengine.com/auth",
          {
            apiKey: "23567b218376f79d9415"
          }
        );
        this.token = response.data.token;
        try {
          const jsonPhotos = await axios.get(
            "http://interview.agileengine.com/images",
            {
              headers: {
                Authorization: "Bearer " + this.token
              }
            }
          );
          this.ready = true;
          for (var i = 0; i < jsonPhotos.data.pictures.length; i++) {
            this.photos.push(jsonPhotos.data.pictures[i]);
          }
          this.page = jsonPhotos.data.page;
        } catch (e) {
          console.log(e);
        }
      } catch (error) {
        console.error(error);
      }
    }
  },
  methods: {
    photoView: async function(id) {
      try {
        const response = await axios.get(
          "http://interview.agileengine.com/images/" + id,
          {
            headers: {
              Authorization: "Bearer " + this.token
            }
          }
        );
        this.photo = response.data;
        this.dialog = true;
      } catch (e) {
        console.log(e);
      }
    }
  },
  watch: {
    stepNum: async function(newStep) {
      try {
        const response = await axios.get(
          "http://interview.agileengine.com/images/" + this.photos[newStep].id,
          {
            headers: {
              Authorization: "Bearer " + this.token
            }
          }
        );
        this.photo = response.data;
      } catch (e) {
        console.log(e);
      }
    }
  }
};
</script>
