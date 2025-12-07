<template>
    <div class="w-full h-screen bg-black relative">
    <!-- <video autoplay muted loop playsinline class="absolute w-full h-full object-cover">
        <source src="/nm-hero-video.mp4" type="video/mp4" />
        Your browser does not support the video tag.
    </video> -->
    <div style="position:absolute;" class="w-full h-full object-cover">
      <iframe 
            loading="lazy" title="Gumlet video player"
            src="https://play.gumlet.io/embed/6935e66f491e9c48bfcbd850?background=true&autoplay=true&loop=true&disableControls=true"
            style="border:none; position: absolute; top: 0; left: 0; height: 100%; width: 100%;"
          referrerpolicy="origin"
          allow="accelerometer; gyroscope; autoplay; encrypted-media; picture-in-picture; fullscreen;">
          </iframe>
    </div>
    <div class="flex flex-col justify-center items-center h-full w-full text-center px-4 bg-black/50 relative z-10">
        <h1 class="text-5xl md:text-7xl max-w-5xl font-medium text-white mb-6  items-center">
            {{ displayText }}<span class="animate-pulse text-amber-400">|</span>
        </h1>
        <p class="text-lg md:text-2xl text-gray-300 mb-8">Discover amazing content and connect with us.</p>
       <AppButton @click="$router.push('/#services')">See Our Services</AppButton>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const texts = [
  "LUXURY LIVING WITHIN REACH",
  "BUY PROPERTIES WITH CERTAINTY",
  "ALL-IN-ONE DIGITAL REALTY SOLUTIONS",
  "CONNECTING PEOPLE, PROPERTY AND TECHNOLOGY"
]

const displayText = ref('')
const currentTextIndex = ref(0)
const currentCharIndex = ref(0)
const isDeleting = ref(false)
let typingTimeout: NodeJS.Timeout

const typeWriter = () => {
  const currentText = texts[currentTextIndex.value]
  
  if (!isDeleting.value) {
    // Typing
    if (currentCharIndex.value < currentText.length) {
      displayText.value = currentText.substring(0, currentCharIndex.value + 1)
      currentCharIndex.value++
      typingTimeout = setTimeout(typeWriter, 80)
    } else {
      // Pause at end before deleting
      typingTimeout = setTimeout(() => {
        isDeleting.value = true
        typeWriter()
      }, 2000)
    }
  } else {
    // Deleting
    if (currentCharIndex.value > 0) {
      displayText.value = currentText.substring(0, currentCharIndex.value - 1)
      currentCharIndex.value--
      typingTimeout = setTimeout(typeWriter, 50)
    } else {
      // Move to next text
      isDeleting.value = false
      currentTextIndex.value = (currentTextIndex.value + 1) % texts.length
      typingTimeout = setTimeout(typeWriter, 500)
    }
  }
}

onMounted(() => {
  typeWriter()
})

onUnmounted(() => {
  if (typingTimeout) {
    clearTimeout(typingTimeout)
  }
})
</script>