<script setup lang="ts">
import { defineComponent } from 'vue'
import Hls from 'hls.js'
import axios from 'axios'
</script>

<script lang="ts">
export default defineComponent({
  data() {
    return {
      isStreaming: false,
      pollInterval: 500,
      streamUrl:
        'https://547f72e6652371c3.mediapackage.us-east-1.amazonaws.com/out/v1/bca94542a67143bc918e801e38d9bd24/index.m3u8',
      intervalId: null as ReturnType<typeof setInterval> | null
    }
  },
  watch: {
    src: {
      immediate: true,
      handler(value) {
        this.startPolling(value)
      }
    }
  },
  methods: {
    async checkStreamStatus(src: string) {
      this.isStreaming = true
      this.initializePlayer(src)
      if (this.intervalId) {
        clearInterval(this.intervalId)
      }
      const liveIndicator = document.getElementById('live-indicator') as HTMLDivElement
      const liveText = document.getElementById('live-text') as HTMLSpanElement
      if (this.isStreaming) {
        liveIndicator.classList.add('bg-red-500')
        liveIndicator.classList.remove('bg-white')
        liveText.textContent = 'Live'
      } else {
        liveIndicator.classList.add('bg-white')
        liveIndicator.classList.remove('bg-red-500')
        liveText.textContent = 'Offline'
      }
      //   try {
      //     await axios.head(src) // Just check if the .m3u8 file is accessible
      //     this.isStreamAvailable = true
      //     this.initializePlayer(src)
      //     if (this.intervalId) {
      //       clearInterval(this.intervalId) // Stop polling if the stream is available
      //     }
      //   } catch (error) {
      //     console.log('Stream not available yet, retrying...', error)
      //   }
    },
    initializePlayer(src: string) {
      const video = document.getElementById('video') as HTMLVideoElement
      if (Hls.isSupported()) {
        const hls = new Hls()
        hls.loadSource(this.streamUrl)
        hls.attachMedia(video)
        hls.on(Hls.Events.MANIFEST_PARSED, () => {
          video.play()
        })
      } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
        video.src = this.streamUrl
        video.addEventListener('loadedmetadata', () => {
          video.play()
        })
      }
    },
    startPolling(src: string) {
      if (this.intervalId) {
        clearInterval(this.intervalId) // Clear existing interval if any
      }
      this.intervalId = setInterval(() => this.checkStreamStatus(src), this.pollInterval)
    }
  },
  beforeDestroy() {
    if (this.intervalId) {
      clearInterval(this.intervalId) // Clean up the interval when component is destroyed
    }
  }
})
</script>

<template>
  <div class="mx-auto mt-5 max-w-7xl px-8 sm:px-6 lg:px-8">
    <div class="flex-col justify-center items-center gap-2">
      <div>
        <video id="video" controls autoplay muted class="w-4/5 aspect-video mx-auto"></video>
      </div>
      <!-- <div class="text-center mt-4">
        <div id="live-indicator" class="inline-block w-4 h-4 mr-2 bg-white rounded-full"></div>
        <span id="live-text" class="text-xl">Offline</span>
      </div> -->
    </div>
  </div>
</template>
