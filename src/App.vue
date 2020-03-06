<template>
  <div id="app">
    <router-view v-if="log" :tokens="tokens" />
    <a v-on:click="connectSpotify()" v-else>You need to accept Spotify access !</a>
  </div>
</template>

<script src="@/js/uikit.min.js"></script>
<script src="@/js/uikit-icons.min.js"></script>
<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      log: false,
      clientId: "c184b656df844968bf7e15a59d6a1797",
      clientSecret: "539d3ade42194de893b130360980e664",
      redirectUrl: "http://localhost:8080/",
      scope:
        "user-read-private user-read-email user-read-playback-state user-modify-playback-state user-read-currently-playing streaming app-remote-control",
      state: "420",
      tokens: Map
    };
  },
  created() {
    let urlParam = window.location.href
      .substring(this.redirectUrl.length + 2)
      .split("&");
    let params = new Map();
    urlParam.forEach(element => {
      const t = element.split("=");
      params.set(t[0], t[1]);
    });

    if (
      params.has("access_token") &&
      params.has("token_type") &&
      params.has("expires_in") &&
      params.has("state")
    ) {
      if (params.get("state") == this.state) {
        console.log("Connected !");
        this.log = true;
        this.tokens = new Map(params);
        // AJOUTER TIMER QUI RELANCE LA CONNEXION QUAND EXPIRE
      }
    } else {
      this.connectSpotify();
    }
  },
  methods: {
    connectSpotify() {
      console.log("Connect to spotify !");
      window.location.replace(
        `https://accounts.spotify.com/authorize?client_id=${this.clientId}&redirect_uri=${this.redirectUrl}&scope=${this.scope}&response_type=token&state=${this.state}`
      );
    }
  }
};
</script>

<style src="@/css/uikit.min.css"></style>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
