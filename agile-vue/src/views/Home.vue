<template>
  <div class="home">
    <h1>Test</h1>
  </div>
</template>

<script>
const axios = require("axios");

export default {
  name: "Home",
  created: async function() {
    let config = {
      name: "Sherlock",
      enemy: "Moriarty"
    };
    axios
      .post("http://interview.agileengine.com/auth", {
        params: config
      })
      .then(response => {
        this.user = response.data;
      })
      .catch(e => {
        console.log(e);
      });
  }
};
</script>
