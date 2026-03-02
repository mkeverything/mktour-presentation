<script setup lang="ts">
import { computed, onBeforeUnmount, ref } from 'vue'

const props = withDefaults(defineProps<{
  sourceLabel?: string
  hint?: string
}>(), {
  sourceLabel: 'Browser tab/window',
  hint: 'Open the target site in another Chrome window, click "Start sharing", then choose that window.',
})

const videoEl = ref<HTMLVideoElement | null>(null)
const stream = ref<MediaStream | null>(null)
const isStarting = ref(false)
const errorMessage = ref('')

const isSharing = computed(() => Boolean(stream.value))

function clearVideo() {
  if (videoEl.value)
    videoEl.value.srcObject = null
}

function stopSharing() {
  if (stream.value) {
    for (const track of stream.value.getTracks())
      track.stop()
  }

  stream.value = null
  clearVideo()
}

async function startSharing() {
  if (isStarting.value || isSharing.value)
    return

  errorMessage.value = ''
  isStarting.value = true

  try {
    const media = await navigator.mediaDevices.getDisplayMedia({
      video: true,
      audio: false,
    })

    const [videoTrack] = media.getVideoTracks()
    if (videoTrack) {
      videoTrack.addEventListener('ended', () => {
        stream.value = null
        clearVideo()
      })
    }

    stream.value = media
    if (videoEl.value) {
      videoEl.value.srcObject = media
      await videoEl.value.play()
    }
  }
  catch (error) {
    const message = error instanceof Error ? error.message : 'Screen capture was denied or failed.'
    errorMessage.value = message
    stopSharing()
  }
  finally {
    isStarting.value = false
  }
}

onBeforeUnmount(() => {
  stopSharing()
})
</script>

<template>
  <div class="mk-share-wrap">
    <div class="mk-share-controls-top">
      <button
        class="mk-share-btn"
        :disabled="isStarting || isSharing"
        @click="startSharing"
      >
        {{ isStarting ? 'Starting...' : 'Start sharing' }}
      </button>
      <button
        class="mk-share-btn mk-share-btn-stop"
        :disabled="!isSharing"
        @click="stopSharing"
      >
        Stop sharing
      </button>
    </div>

    <div class="mk-share-stage">
      <video
        ref="videoEl"
        class="mk-share-video"
        autoplay
        muted
        playsinline
      />
      <div v-if="!isSharing" class="mk-share-placeholder">
        <p class="mk-share-placeholder-title">
          Live demo source: <strong>{{ sourceLabel }}</strong>
        </p>
        <p>{{ hint }}</p>
      </div>
    </div>

    <p v-if="errorMessage" class="mk-share-error">
      {{ errorMessage }}
    </p>
  </div>
</template>
