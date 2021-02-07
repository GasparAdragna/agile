<template>
  <div class="home">
    <v-row v-if="!ready">
      <v-col cols="12" justify="center" class="text-center">
        <v-progress-circular
          :size="50"
          color="primary"
          indeterminate
          class="text-center"
        ></v-progress-circular>
        <h2>Loading photos...</h2>
      </v-col>
    </v-row>
    <v-row v-if="ready">
      <v-col v-for="(photo, index) in photos" :key="photo.id" cols="12" md="4">
        <a @click="photoView(photo.id, index)">
          <v-img :src="photo.cropped_picture"></v-img
        ></a>
      </v-col>
      <v-btn color="primary" @click="morePhotos()" block :disabled="disabled">
        Load more photos
      </v-btn>
    </v-row>
    <br />
    <v-dialog v-model="dialog">
      <v-carousel v-model="stepNum" height="100%" :hide-delimiters="true">
        <v-carousel-item
          v-for="(item, i) in photos"
          :key="i"
          reverse-transition="fade-transition"
          transition="fade-transition"
          justify="center"
          class="text-center"
        >
          <div v-if="photoReady">
            <img :src="photo.full_picture" style="height:auto;" />
            <v-overlay absolute class="d-flex align-end" opacity="0">
              <p><b>Author: </b>{{ photo.author }}</p>
              <p><b>Camera: </b>{{ photo.camera }}</p>
              <p><b>Tags: </b>{{ photo.tags }}</p>
            </v-overlay>
          </div>
          <div v-else>
            <v-progress-circular
              :size="50"
              color="primary"
              indeterminate
              class="text-center"
            ></v-progress-circular>
            <h2>Loading photo...</h2>
          </div>
        </v-carousel-item>
      </v-carousel>
    </v-dialog>
  </div>
</template>

<script>
const axios = require("axios");
const Swal = require("sweetalert2");

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
      photoReady: false,
      stepNum: 0,
      disabled: false,
      currentPhoto: 0
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
          Swal.fire({
            icon: "error",
            title: "Oops...",
            text: "Something went wrong while trying to autheticate you.",
            footer: "Please refresh the page and try again"
          });
        }
      } catch (error) {
        Swal.fire({
          icon: "error",
          title: "Oops...",
          text: "You already have a token"
        });
      }
    }
  },
  methods: {
    photoView: async function(id, index) {
      this.photoReady = false;
      this.currentPhoto = index;
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
        this.photoReady = true;
      } catch (e) {
        Swal.fire({
          icon: "error",
          title: "Oops...",
          text: "Something went wrong while trying to load the photo",
          footer: "Please try again in a couple of minutes"
        });
      }
    },
    async morePhotos() {
      this.disabled = true;
      this.page++;
      try {
        const response = await axios.get(
          "http://interview.agileengine.com/images/?page=" + this.page,
          {
            headers: {
              Authorization: "Bearer " + this.token
            }
          }
        );
        for (var i = 0; i < response.data.pictures.length; i++) {
          this.photos.push(response.data.pictures[i]);
        }
        this.page = response.data.page;
        this.disabled = false;
      } catch (e) {
        Swal.fire({
          icon: "error",
          title: "Oops...",
          text: "Something went wrong while trying to load more photos",
          footer: "Please try again in a couple of minutes"
        });
      }
    }
  },
  watch: {
    stepNum: async function(nextStep, oldStep) {
      this.photoReady = false;
      nextStep > oldStep ? this.currentPhoto++ : this.currentPhoto--;
      try {
        const response = await axios.get(
          "http://interview.agileengine.com/images/" +
            this.photos[this.currentPhoto].id,
          {
            headers: {
              Authorization: "Bearer " + this.token
            }
          }
        );
        this.photo = response.data;
        this.photoReady = true;
      } catch (e) {
        Swal.fire({
          icon: "error",
          title: "Oops...",
          text: "Something went wrong while trying to load the photo",
          footer: "Please try again in a couple of minutes"
        });
      }
    }
  }
};
</script>
