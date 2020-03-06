<template>
  <div class="home">
    <Navbar :username="user.display_name" :profileLink="user.external_urls.spotify"/>

    <div id="research" class="uk-column-1-4">
      <div id="tracks" v-if="research.tracks">
          <h4 style="color: #aa1155;">Musiques</h4>
          <ul class="uk-list uk-list-large uk-list-divider">
            <li v-for="track in research.tracks.items" v-bind:key="track.id" style="height: 60px;"><img :src="track.album.images[2].url" style="width: 60px; height: 60px;" class="uk-align-left"> <span style="font-weight: bold;">{{ track.name }}</span><br><span style="font-style: italic;">{{ track.artists[0].name }}</span></li> <!-- 60px*60px -->
          </ul>
      </div>
      <div id="artists" v-if="research.artists">
        <h4 style="color: #880044;">Artistes</h4>
          <ul class="uk-list uk-list-large uk-list-divider">
            <li v-for="artist in research.artists.items" v-bind:key="artist.id" style="height: 60px;"><img :src="getArtistImage(artist)" style="width: 60px; height: 60px;" class="uk-align-left"> <span style="font-weight: bold;">{{ artist.name }}</span></li> <!-- 160px*160px -->
          </ul>
      </div>
      <div id="albums" v-if="research.albums">
        <h4 style="color: #dd1155;">Albums</h4>
          <ul class="uk-list uk-list-large uk-list-divider">
            <li v-for="album in research.albums.items" v-bind:key="album.id" style="height: 60px;"><img :src="album.images[2].url" style="width: 60px; height: 60px;" class="uk-align-left"> <span style="font-weight: bold;">{{ album.name }}</span></li>  <!-- 60px*60px -->
          </ul>
      </div>
      <div id="playlists" v-if="research.playlists">
        <h4 style="color: #d44d5c;">Playlists</h4>
          <ul class="uk-list uk-list-large uk-list-divider">
            <li v-for="playlist in research.playlists.items" v-bind:key="playlist.id" style="height: 60px;"><img :src="getPlaylistImage(playlist)" style="width: 60px; height: 60px;" class="uk-align-left"> <span style="font-weight: bold;">{{ playlist.name }}</span></li> <!-- 300px*300px -->
          </ul>
      </div>
    </div>

  </div>
</template>

<script>
// @ is an alias to /src
import Navbar from '@/components/Navbar.vue'

export default {
  name: 'Home',
  components: {
    Navbar
  },
  data () {
    return {
      tokens: this.$parent.tokens,
      user: JSON,
      research: { }
    }
  },
  created () {
    this.getUser()
  },
  methods: {
    getUser () {
      // https://api.spotify.com/v1/me
      // Headers
      const headers = new Headers()
      headers.set('Authorization', `Bearer ${this.tokens.get('access_token')}`)
      // Get User Profile
      const initReq = {
        method: 'GET',
        headers: headers
      }
      fetch('https://api.spotify.com/v1/me', initReq).then(res => {
        res.json().then(data => {
          this.user = data
        })
      })
    },
    searchItem (search) {
      // https://api.spotify.com/v1/search
      // Headers
      const headers = new Headers()
      headers.set('Authorization', `Bearer ${this.tokens.get('access_token')}`)
      // Get User Profile
      const initReq = {
        method: 'GET',
        headers: headers
      }
      const types = 'album,artist,playlist,track'
      const url = `https://api.spotify.com/v1/search?q=${search}&type=${types}&limit=15`

      fetch(url, initReq).then(res => {
        res.json().then(data => {
          this.research = data
        })
      })
    },
    getArtistImage (artist) {
      if (artist.images[2]) {
        return artist.images[2].url
      } else {
        return 'https://cdn1.iconfinder.com/data/icons/user-pictures/100/unknown-512.png'
      }
    },
    getPlaylistImage (playlist) {
      if (playlist.images[0]) {
        return playlist.images[0].url
      } else {
        return 'https://www.kindpng.com/picc/m/65-652114_video-file-data-playlist-play-music-phone-icon.png'
      }
    }
  }
}
</script>
