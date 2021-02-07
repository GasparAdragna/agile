<template>
  <div class="home">
    <v-row>
      <v-col v-for="photo in photos" :key="photo.id" cols="12" md="4">
        <v-img :src="photo.cropped_picture"></v-img>
      </v-col>
    </v-row>
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
      page: 0
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
  }
};
</script>
