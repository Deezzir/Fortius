<script setup lang="ts">
import { defineComponent } from 'vue'
import Hls from 'hls.js'
</script>

<script lang="ts">
export default defineComponent({
  mounted() {
    const video = document.getElementById('video') as HTMLVideoElement
    const src = 'https://bitdash-a.akamaihd.net/content/sintel/hls/playlist.m3u8'
    if (Hls.isSupported()) {
      var hls = new Hls()
      hls.loadSource(src)
      hls.attachMedia(video)
    } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
      video.src = src
    }
  }
})
</script>

<template>
  <div class="mx-auto mt-5 max-w-7xl px-2 sm:px-6 lg:px-8">
    <div class="flex-col justify-center items-center gap-2">
      <div class="aspect-w-16 aspect-h-9 w-full">
        <video id="video" controls class="w-full h-full object-cover"></video>
      </div>
      <div class="shrink text-left">
        <div id="live-indicator" class="inline-block w-4 h-4 mr-3 bg-red-500 rounded-full"></div>
        <span id="live-text" class="text-xl">Live</span>
      </div>
    </div>
  </div>
</template>
