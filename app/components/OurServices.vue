<template>
    <div class="p-4 md:p-10">
    <div class="w-full flex flex-col items-center p-4 md:p-10 bg-black md:!py-20">
    <AppPretitle>Our Services</AppPretitle>
    <AppSecondaryHeader>What We Offer</AppSecondaryHeader>
    
    <div class="w-full max-w-screen-2xl mt-12 h-[800px] flex">
      <div 
        v-for="(service, index) in services" 
        :key="index"
        class="relative overflow-hidden transition-all duration-700 ease-in-out cursor-pointer group"
        :class="hoveredIndex === index ? 'flex-[3]' : 'flex-[0.5]'"
        @mouseenter="hoveredIndex = index"
      >
        <!-- Background overlay -->
        <div class="absolute inset-0 bg-gradient-to-t from-black/90 via-black/50 to-transparent z-10"></div>
        
        <!-- Background image (placeholder) -->
        <div class="absolute inset-0 bg-gradient-to-br from-amber-600 to-amber-900 opacity-50"></div>
        
        <!-- Content -->
        <div class="relative z-20 h-full p-6 flex flex-col justify-end">
          <!-- Title (always visible) -->
          <h3 
            class="text-white font-bold transition-all duration-700 origin-bottom-left"
            :class="hoveredIndex === index ? 'text-xl md:text-3xl mb-4' : 'text-lg md:text-2xl whitespace-nowrap transform -rotate-90 translate-y-0 translate-x-2 md:translate-x-5'"
          >
            {{ service.title }}
          </h3>
          
          <!-- Description (only visible when expanded) -->
          <div 
            class="overflow-hidden transition-all duration-700"
            :class="hoveredIndex === index ? 'max-h-[300px] opacity-100' : 'max-h-0 opacity-0'"
          >
            <p class="text-gray-200 text-base max-w-xl leading-relaxed">
              {{ service.description }}
            </p>
          </div>
        </div>
      </div>
    </div>
    </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const hoveredIndex = ref(0) // Default first item expanded

const services = [
  {
    title: "Property Sales",
    description: "Nouvelle Maison connects homebuyers to their properties of choice in a seamless and professional manner. We offer varied and competitive property listings which are tailored to meet each buyer's taste, lifestyle and budget."
  },
  {
    title: "Rentals",
    description: "We offer technology-driven rental solutions that help renters acquire luxury apartments with ease and certainty by matching them to credible property developers and homeowners. Our rental system supports landlords with vacant property advertisements, applicant filtering and occupancy efficient assessment. Also, we manage the commercial side of property acquisition and rental by delivering optimal buy-to-let investment returns for our clients."
  },
  {
    title: "Property Advisory",
    description: "We provide independent strategic advice to aspiring homeowners, property developers, REITs, landlords, and agents in areas of due diligence, property valuation, new space acquisition, compliance and risk management."
  },
  {
    title: "Property Development",
    description: "Nouvelle Maison builds custom-made luxury apartments in the most distinctive residential and business addresses in Ghana. We create refreshing property designs that offer clients rich living experiences that meet lifestyle goals and aspirations. Clients with existing properties can benefit from renovation and remodelling services."
  }
]
</script>