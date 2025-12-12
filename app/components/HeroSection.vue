<template>
    <!-- Outer container defines the scrollable track/height. 
         Increased to 500vh to allow enough space for 2 separate text animations + button reveal. -->
    <div ref="containerRef" class="relative w-full h-[500vh] bg-black">
        <!-- Sticky container stays pinned while scrolling locally -->
        <div class="sticky top-0 h-screen w-full overflow-hidden flex flex-col items-center justify-center">
            
            <!-- Background Video -->
            <div class="absolute inset-0 w-full h-full z-0">
                 <video autoplay muted loop playsinline class="w-full h-full object-cover">
                    <source src="https://video.gumlet.io/680ac81b0527a5bd8dda23eb/6936c2f53dd43f39be928afa/download.mp4" type="video/mp4" />
                    Your browser does not support the video tag.
                </video>
            </div>

            <!-- Content Layer -->
            <div class="relative z-10 flex flex-col justify-center items-center h-full w-full text-center px-4 md:px-10 pointer-events-none">
                
                <!-- Text 1 Container -->
                <div 
                    class="absolute inset-0 flex items-center justify-center origin-center will-change-transform"
                    :style="{ transform: `scale(${text1Scale})`, opacity: text1Opacity }"
                >
                    <h1 class="text-3xl md:text-7xl max-w-5xl font-medium text-white items-center text-center">
                        LUXURY LIVING <br /> WITHIN REACH
                    </h1>
                </div>

                <!-- Text 2 Container -->
                <div 
                    class="absolute inset-0 flex items-center justify-center origin-center will-change-transform"
                    :style="{ transform: `scale(${text2Scale})`, opacity: text2Opacity }"
                >
                     <h1 class="text-3xl md:text-7xl max-w-5xl font-medium text-white items-center text-center">
                        ALL-IN-ONE DIGITAL <br /> REALTY SOLUTIONS
                    </h1>
                </div>

                <!-- Static Content (Button/Desc) -->
                <!-- Need pointer-events-auto here because parent has pointer-events-none to let scroll pass through if needed, 
                     but actually sticky container captures scroll naturally. 
                     We need pointer input for the button though. -->
                <div 
                    class="absolute top-1/2 w-full mt-24 md:mt-32 flex flex-col items-center pointer-events-auto"
                    :style="{ opacity: subContentOpacity, transform: `translateY(${subContentTranslate}px)` }"
                >
                    <p class="text-lg hidden md:text-2xl text-gray-300 mb-8 max-w-3xl">
                        Welcome to Nouvelle Maison crafted for comfort, style, and effortless luxury.
                    </p>
                    <AppButton @click="$router.push('/#services')">See Our Services</AppButton>
                </div>
            </div>

            <div class="max-w-60 absolute bottom-20 md:left-6 left-4 bg-black/50 backdrop-blur-sm p-2 z-20 flex flex-col items-center gap-2">
                <img src="https://video.gumlet.io/680ac81b0527a5bd8dda23eb/693809857aaaf18e07b25182/thumbnail-1-0.png?v=1765280353738" class="w-full h-18 md:h-full object-cover" alt="">
                <AppButton @click="openVideoModal" class="w-full relative">Watch Promo Video</AppButton>
                <PromoVideo @close="closeVideoModal" v-if="isVideoModalOpen" />
            </div>
            <!-- Marquee Footer -->
            <div class="absolute bottom-0 w-full h-16 bg-black/50 backdrop-blur-sm flex items-center overflow-x-hidden z-20 pointer-events-auto">
                <div class="marquee-content-1 whitespace-nowrap flex items-center absolute top-0 py-4">
                    <span v-for="(text, index) in newText" :key="index" class="text-white text-base md:text-2xl font-light tracking-widest mx-4">
                        {{ text }} <span class="text-amber-400 mx-4">*</span>
                    </span>
                     <span v-for="(text, index) in newText" :key="index + 'dup'" class="text-white text-base md:text-2xl font-light tracking-widest mx-4">
                        {{ text }} <span class="text-amber-400 mx-4">*</span>
                    </span>
                </div>
                 <div class="marquee-content-2 whitespace-nowrap flex items-center absolute top-0 py-4">
                    <span v-for="(text, index) in newText" :key="index" class="text-white text-base md:text-2xl font-light tracking-widest mx-4">
                        {{ text }} <span class="text-amber-400 mx-4">*</span>
                    </span>
                     <span v-for="(text, index) in newText" :key="index + 'dup2'" class="text-white text-base md:text-2xl font-light tracking-widest mx-4">
                        {{ text }} <span class="text-amber-400 mx-4">*</span>
                    </span>
                </div>
            </div>

        </div>
    </div>
</template>

<style scoped>
.marquee-content-1 {
  animation: marquee 25s linear infinite;
}

.marquee-content-2 {
  animation: marquee2 25s linear infinite;
}

@keyframes marquee {
  0% { transform: translateX(0%); }
  100% { transform: translateX(-100%); }
}

@keyframes marquee2 {
  0% { transform: translateX(100%); }
  100% { transform: translateX(0%); }
}
</style>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

const newText = [
  "NOUVELLE MAISON",
  "Crafted for comfort, style, and effortless luxury",
]

// --- Scroll Animation Logic ---
const containerRef = ref<HTMLElement | null>(null)

// Text 1 State
const text1Scale = ref(150) 
const text1Opacity = ref(0) // Start invisible until we process scroll, or simpler: start 0.

// Text 2 State
const text2Scale = ref(100)
const text2Opacity = ref(0)

// SubContent State
const subContentOpacity = ref(0)
const subContentTranslate = ref(50)

// Helper to map range [start, end] to [0, 1]
const mapRange = (value: number, start: number, end: number) => {
    if (value < start) return 0;
    if (value > end) return 1;
    return (value - start) / (end - start);
}

const handleScroll = () => {
  if (!containerRef.value) return

  const rect = containerRef.value.getBoundingClientRect()
  const viewportHeight = window.innerHeight
  // The distance the sticky container can travel is (containerHeight - viewportHeight)
  // But progress is based on how far we've scrolled into the container.
  // containerRect.top goes from 0 down to -(height - viewportHeight).
  const totalTravel = rect.height - viewportHeight
  
  let progress = -rect.top / totalTravel
  progress = Math.max(0, Math.min(1, progress))

  // --- Animation Ranges ---
  // 1. Text 1 zooms in: 0.0 -> 0.35
  // 2. Text 1 fades out: 0.35 -> 0.45
  // 3. Text 2 zooms in: 0.45 -> 0.8
  // 4. Sub content fades in: 0.8 -> 1.0

  // --- Text 1 Logic ---
  const t1ZoomProgress = mapRange(progress, 0.0, 0.35)
  // Scale 50 -> 1
  text1Scale.value = 150 - (t1ZoomProgress * 149)
  
  // Fade IN logic for Text 1? 
  // It should probably just be visible from start? 
  // Or maybe fade in quickly at very start? Lets say visible if progress < 0.35 logic handled below.
  
  const t1FadeOutProgress = mapRange(progress, 0.35, 0.45)
  if (progress < 0.35) {
      text1Opacity.value = 1
  } else {
      text1Opacity.value = 1 - t1FadeOutProgress // 1 -> 0
  }

  // --- Text 2 Logic ---
  const t2ZoomProgress = mapRange(progress, 0.45, 0.8)
  // Scale 50 -> 1
  text2Scale.value = 100 - (t2ZoomProgress * 99)
  
  // Opacity for Text 2
  // It should fade in or just pop in and zoom? 
  // Usually better to have it opaque once it starts zooming?
  // Or fade in while zooming?
  // Let's make it fully visible as soon as the zoom phase starts (0.45), 
  // but maybe we want it to be 0 before that.
  if (progress < 0.45) {
      text2Opacity.value = 0
  } else {
      text2Opacity.value = 1
  }
  
  // --- Sub-Content Logic ---
  const subProgress = mapRange(progress, 0.8, 0.95)
  subContentOpacity.value = subProgress
  subContentTranslate.value = 50 * (1 - subProgress)
}

const isVideoModalOpen = ref(false)

const openVideoModal = () => {
    isVideoModalOpen.value = true
}

const closeVideoModal = () => {
    isVideoModalOpen.value = false
}

onMounted(() => {
  window.addEventListener('scroll', handleScroll, { passive: true })
  handleScroll() // Initial calc
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})
</script>