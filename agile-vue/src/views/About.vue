<template>
  <div class="about">
    <v-row>
      <v-col cols="12" justify="center" align="center">
        <v-avatar width="400" height="300">
          <img src="http://soygaspar.com.ar/imgs/gaspar.jpg" alt="Gaspar" />
        </v-avatar>
        <h1>Gaspar Adragna</h1>
        <p class="mb-1">
          This is my <b>attempt</b> at the challenge that was sent to me.
        </p>
        <p>
          I think a lot can be improved but for such little timme i focused on
          <b>achieving</b> the goal.
        </p>
        <p>
          Some of the aspects i think could be <b>done better</b> or
          <b>be improved</b> for another time are:
        </p>
        <ul>
          <li>
            <b>Better Authorization check and renewal</b>. At this time every
            time the user refreshes the page a new token is achieved. It would
            be a lot better to store the current token and only ask for a new
            one once the current one expires
          </li>
          <li>
            Adding Vuex to store the credentials or a cookie.
          </li>
          <li>
            <b> Nicer user experience</b>. Again, for such little time, i tried
            to used components from <b>Vuetify</b> to speed the front- end
            process. More colors, a footer and much more could be beneficial for
            the whole user experience
          </li>
          <li>
            The whole carousel experience is not the greatest, but it gets the
            job done.
          </li>
        </ul>
        <p>
          Overall i thin it was a nice challenge and i hope i was able to
          achieve the goal of the excercise.
        </p>
        <p>
          <b>For any questions or inquires</b> please feel free to contact me
          at:
          <a href="mailto:gasparadragna@gmail.com">gasparadragna@gmail.com</a>
        </p>
      </v-col>
    </v-row>
  </div>
</template>
