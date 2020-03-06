<template>
  <div id="player">
    <div class="player-item">
      <a>
        <i class="fas fa-angle-double-left" style="font-size: 35px; color: blue;"></i>
      </a>
    </div>
    <div class="player-item">
      <a v-on:click="playing = !playing" style="position: relative; top: -2px;">
        <i class="fas fa-pause" style="font-size: 28px; color: red;" v-if="playing"></i>
        <i class="fas fa-play" style="font-size: 28px; color: green;" v-else></i>
      </a>
    </div>
    <div class="player-item">
      <a>
        <i class="fas fa-angle-double-right" style="font-size: 35px; color: purple;"></i>
      </a>
    </div>

    <div class="player-item">
      <img :src="getImage()" style="width: 60px; height: 60px;" />
    </div>
    <div class="player-item" v-if="actualPlay != null">
      <span style="font-weight: bold;">{{ actualPlay.name }}</span>
      <br />
      <span style="font-style: italic;">{{ actualPlay.artists[0].name }}</span>
    </div>
    <div class="player-item" v-else>Titre - Artiste</div>
    <div class="player-item">TimeBar</div>
    <div class="player-item">{{ timer }} / {{ duration }}</div>
  </div>
</template>

<script>
export default {
  name: "Player",
  data() {
    return {
      playing: false,
      timer: "00:00",
      duration: "00:00",
      actualPlay: null,
      spotifyPlayer: null,
      deviceId: '',
      token: ''
    };
  },
  props: {
    tracksQ: Array
  },
  methods: {
    getImage() {
      if (this.actualPlay != null && this.actualPlay.album.images[2].url) {
        return this.actualPlay.album.images[2].url;
      } else {
        return "https://w1.pngwave.com/png/197/883/597/weed-leaf-green-plant-logo-hemp-family-symbol-png-clip-art.png";
      }
    },
    startTrack() {
      console.log("start track");
    
      fetch(`https://api.spotify.com/v1/me/player/play?device_id=${this.deviceId}`, {
            method: "PUT",
            body: JSON.stringify({ uris: [this.actualPlay.uri] }),
            headers: {
              "Content-Type": "application/json",
              Authorization: `Bearer ${this.token}`
            }
          });

    }
  },
  watch: {
    tracksQ: function() {
      if (this.actualPlay == null) {
        this.actualPlay = this.tracksQ[0];
        this.startTrack();
      }
    }
  },
  created() {
    let spotifySdk = document.createElement("script");
    spotifySdk.setAttribute("src", "https://sdk.scdn.co/spotify-player.js");
    document.head.appendChild(spotifySdk);

    console.log("start player");
    window.onSpotifyWebPlaybackSDKReady = () => {
      const token = this.$parent.$parent.tokens.get("access_token");
      this.token = token

      this.spotifyPlayer = new Spotify.Player({
        name: "Spotappy",
        getOAuthToken: cb => {
          cb(token);
        },
        volume: 0.5
      });

      // Error handling
      this.spotifyPlayer.addListener("initialization_error", ({ message }) => {
        console.error(message);
      });
      this.spotifyPlayer.addListener("authentication_error", ({ message }) => {
        console.error(message);
      });
      this.spotifyPlayer.addListener("account_error", ({ message }) => {
        console.error(message);
      });
      this.spotifyPlayer.addListener("playback_error", ({ message }) => {
        console.error(message);
      });

      // Playback status updates
      this.spotifyPlayer.addListener("player_state_changed", state => {
        console.log(state);
      });

      // Ready
      this.spotifyPlayer.addListener("ready", ({ device_id }) => {
        this.deviceId = device_id
        console.log("Ready with Device ID", device_id);
      });

      // Not Ready
      this.spotifyPlayer.addListener("not_ready", ({ device_id }) => {
        console.log("Device ID has gone offline", device_id);
      });

      // Connect to the player!
      this.spotifyPlayer.connect().then(success => {
        if (success) console.log("Connected succesfully !");
      });
    };
  },
  beforeDestroy() {
    if (this.spotifyPlayer != null) {
      this.spotifyPlayer.disconnect();
    }
  }
};
</script>

<style scoped>
.player-item {
  display: inline-block;
  margin-right: 20px;
}
</style>
