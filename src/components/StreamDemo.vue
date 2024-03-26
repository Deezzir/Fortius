<script setup lang="ts">
import { defineComponent } from 'vue'
import Hls from 'hls.js'
import axios from 'axios'
</script>

<script lang="ts">
export default defineComponent({
  data() {
    return {
      isStreamAvailable: false,
      pollInterval: 3000,
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
      try {
        await axios.head(src) // Just check if the .m3u8 file is accessible
        this.isStreamAvailable = true
        this.initializePlayer(src)
        if (this.intervalId) {
          clearInterval(this.intervalId) // Stop polling if the stream is available
        }
      } catch (error) {
        console.log('Stream not available yet, retrying...', error)
      }
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
  //   data() {
  //     return {
  //       streamActive: false
  //     }
  //   },
  //   watch: {
  //     streamActive() {
  //       const liveIndicator = document.getElementById('live-indicator') as HTMLDivElement
  //       const liveText = document.getElementById('live-text') as HTMLSpanElement
  //       if (this.streamActive) {
  //         liveIndicator.classList.remove('bg-red-500')
  //         liveIndicator.classList.add('bg-green-500')
  //         liveText.textContent = 'Live'
  //       } else {
  //         liveIndicator.classList.remove('bg-green-500')
  //         liveIndicator.classList.add('bg-red-500')
  //         liveText.textContent = 'Offline'
  //       }
  //     }
  //   },
  //   methods: {
  //     checkStreamStatus() {
  //       fetch(this.streamUrl)
  //         .then((response) => {
  //           if (response.ok) {
  //             this.streamActive = true
  //           } else {
  //             this.streamActive = false
  //           }
  //           console.log('Stream status checked')
  //         })
  //         .catch(() => {
  //           this.streamActive = false
  //         })
  //     },
  //     startPlayer() {
  //       const video = document.getElementById('video') as HTMLVideoElement
  //       if (Hls.isSupported()) {
  //         const hls = new Hls()
  //         hls.loadSource(this.streamUrl)
  //         hls.attachMedia(video)
  //         hls.on(Hls.Events.MANIFEST_PARSED, () => {
  //           video.play()
  //           this.checkStreamStatus()
  //         })
  //       } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
  //         video.src = this.streamUrl
  //         video.addEventListener('loadedmetadata', () => {
  //           video.play()
  //           this.checkStreamStatus()
  //         })
  //       }
  //     }
  //   }
})
</script>

<template>
  <div class="mx-auto mt-5 max-w-7xl px-8 sm:px-6 lg:px-8">
    <div class="flex-col justify-center items-center gap-2">
      <div class="aspect-w-16 aspect-h-9 w-full">
        <video id="video" controls autoplay class="w-full h-full object-cover"></video>
      </div>
      <div class="shrink text-left mt-5">
        <div id="live-indicator" class="inline-block w-4 h-4 mr-2 bg-red-500 rounded-full"></div>
        <span id="live-text" class="text-xl">Live</span>
      </div>
    </div>
  </div>
</template>
