<template>
  <content-with-heading>
    <template slot="heading-left">
      <p class="title is-4">{{ artist.name }}</p>
    </template>
    <template slot="heading-right">
      <a class="button is-small is-dark is-rounded" @click="play">
        <span class="icon"><i class="mdi mdi-shuffle"></i></span> <span>Shuffle</span>
      </a>
    </template>
    <template slot="content">
      <p class="heading has-text-centered-mobile">{{ artist.album_count }} albums | <a class="has-text-link" @click="open_tracks">{{ artist.track_count }} tracks</a></p>
      <list-item-album v-for="album in albums.items" :key="album.id" :album="album"></list-item-album>
    </template>
  </content-with-heading>
</template>

<script>
import { LoadDataBeforeEnterMixin } from './mixin'
import ContentWithHeading from '@/templates/ContentWithHeading'
import ListItemAlbum from '@/components/ListItemAlbum'
import webapi from '@/webapi'

const artistData = {
  load: function (to) {
    return Promise.all([
      webapi.library_artist(to.params.artist_id),
      webapi.library_albums(to.params.artist_id)
    ])
  },

  set: function (vm, response) {
    vm.artist = response[0].data
    vm.albums = response[1].data
  }
}

export default {
  name: 'PageArtist',
  mixins: [ LoadDataBeforeEnterMixin(artistData) ],
  components: { ContentWithHeading, ListItemAlbum },

  data () {
    return {
      artist: {},
      albums: {}
    }
  },

  methods: {
    open_tracks: function () {
      this.$router.push({ path: '/music/artists/' + this.artist.id + '/tracks' })
    },

    play: function () {
      webapi.player_play_uri(this.albums.items.map(a => a.uri).join(','), true)
    }
  }
}
</script>

<style>
</style>
