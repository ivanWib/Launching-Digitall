<template>
  <div class="min-h-screen bg-black text-white overflow-hidden">
    <!-- Initial Landing -->
    <div
      v-if="currentState === 'initial'"
      class="min-h-screen flex flex-col items-center justify-center space-y-8"
    >
      <h1 class="text-5xl font-bold">digitall</h1>
      <button
        @click="startSequence"
        class="px-8 py-4 bg-cyan-500 hover:bg-cyan-600 rounded-lg text-xl font-semibold transform hover:scale-105 transition-all"
      >
        Launch Application
      </button>
    </div>

    <!-- Loading Progress -->
    <div
      v-if="currentState === 'loading'"
      class="min-h-screen flex flex-col items-center justify-center"
    >
      <div class="w-64 h-2 bg-gray-700 rounded-full overflow-hidden">
        <div
          class="h-full bg-cyan-500 transition-all duration-100"
          :style="{ width: `${loadingProgress}%` }"
        ></div>
      </div>
      <div class="mt-4 text-xl">{{ loadingProgress }}%</div>
    </div>

    <!-- Video -->
    <div
      v-if="currentState === 'video'"
      class="video-container absolute inset-0 flex items-center justify-center transition-all duration-1000"
      :class="{
        'opacity-0 scale-90': videoHidden,
        'opacity-100 scale-100': !videoHidden,
      }"
    >
      <video
        class="video-element"
        autoplay
        muted
        playsinline
        @ended="onVideoEnd"
      >
        <source src="@/assets/pemandangan.mp4" type="video/mp4" />
        Your browser does not support the video tag.
      </video>
    </div>

    <!-- Mission Success -->
    <div
      v-if="currentState === 'complete'"
      class="min-h-screen flex flex-col items-center justify-center space-y-8"
    >
      <CheckIcon class="w-32 h-32 text-green-500" />
      <h2 class="text-6xl font-bold text-cyan-500">Mission Accomplished</h2>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { gsap } from "gsap";
import { LoaderIcon, CheckIcon } from "lucide-vue-next";

const currentState = ref("initial");
const loadingProgress = ref(0);
const videoHidden = ref(false); // For controlling video transitions

const startSequence = async () => {
  currentState.value = "loading";

  // Simulate loading animation
  await new Promise((resolve) => {
    gsap.to(loadingProgress, {
      value: 100,
      duration: 3,
      ease: "power1.inOut",
      onUpdate: () => {
        loadingProgress.value = Math.floor(
          gsap.getProperty(loadingProgress, "value")
        );
      },
      onComplete: resolve,
    });
  });

  // Transition to video after loading
  currentState.value = "video";
};

// Handle video end event with exit transition
const onVideoEnd = async () => {
  videoHidden.value = true; // Start fade-out transition
  await new Promise((resolve) => setTimeout(resolve, 1000)); // Wait for fade-out to complete
  currentState.value = "complete";
};
</script>

<style scoped>
/* Styling for video container with better transitions */
.video-container {
  opacity: 0;
  transform: scale(0.9);
  will-change: opacity, transform;
  transition: opacity 1s ease-in-out, transform 1s ease-in-out;
}

.video-container.opacity-100.scale-100 {
  opacity: 1;
  transform: scale(1);
}

.video-container.opacity-0.scale-90 {
  opacity: 0;
  transform: scale(0.9);
}

/* Styling to make video responsive and fit full screen */
.video-element {
  width: 100vw;
  height: 100vh;
  object-fit: cover; /* Maintain aspect ratio */
  display: block;
}
</style>
