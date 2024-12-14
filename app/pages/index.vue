<template>
  <v-layout class="rounded rounded-md">
    <v-navigation-drawer>
      <v-list-item title="Musique Approximative"></v-list-item>
      <v-divider></v-divider>
      <v-list-item 
        v-for="playlist in playlists" 
        :key="playlist.c" 
        link 
        :title="playlist.name"
        @click="fetchTracks(playlist)" 
      ></v-list-item>
    </v-navigation-drawer>
    <v-main class="d-flex align-center justify-center" style="min-height: 300px;">
      <v-data-table 
        :headers="headers" 
        :items="currentPlaylist ? currentPlaylist.tracks : []" />
    </v-main>
  
  </v-layout>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

interface Playlist {
  name: string;
  c: string;
  tracks?: { artist: string; title: string }[]; // Optional tracks property
}

export default defineComponent({
  data() {
    return {
      headers: [
        { title: 'Artist', key: 'artist' },
        { title: 'Title', key: 'title' },
      ],
      playlists: [{
        name: 'Bertier',
        c: 'bertier',
      }, {
        name: 'Nota',
        c: 'nota',
      }] as Playlist[], // Initialize as an empty array of Playlist type
      currentPlaylist: null as Playlist | null, // Use null or undefined
    };
  },
  methods: {
    async fetchTracks(playlist: { name: string; c: string; tracks?: { artist: string; title: string; }[] | undefined; } | null) {
      try {
        const response = await fetch(`https://www.musiqueapproximative.net/posts?format=json&c=${playlist.c}`); 
        const data = await response.json();
        this.currentPlaylist = {
          ...playlist,
          tracks: data.posts
            .filter((post) => post.track) // Filter out posts without a track property
            .map((post) => ({
              artist: post.track?.author ?? 'Unknown Artist', // Use optional chaining and nullish coalescing
              title: post.track?.title ?? 'Unknown Title', // Use optional chaining and nullish coalescing
            })),
        };
      } catch (error) {
        console.error('Error fetching tracks:', error);
      }
    },
  }
});
</script>
