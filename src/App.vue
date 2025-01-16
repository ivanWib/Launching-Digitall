<template>
  <div class="min-h-screen bg-black text-white">
    <!-- Initial Landing -->
    <div
      v-if="currentState === 'initial'"
      class="relative flex flex-col items-center justify-center min-h-screen bg-gradient"
    >
      <!-- Decorative Elements -->
      <div class="absolute inset-0 bg-overlay"></div>

      <!-- Logo -->
      <img
        src="@/assets/digitall.png"
        alt="Digitall Logo"
        class="relative z-10 w-80"
      />

      <!-- Main Content -->
      <div class="relative z-10 text-center max-w-3xl px-6">
        <h1 class="text-5xl font-extrabold mb-4">
          Welcome to <span class="text-accent">DIGITALL</span>
        </h1>
        <p class="text-lg text-secondary mb-5">
          A revolutionary step in digital transformation. Join us as we unveil
          an innovative solution designed to elevate efficiency and transparency
          in government services.
        </p>
        <button
          @click="startSequence"
          class="w-80 h-80 bg-accent text-white text-lg font-semibold rounded-full shadow-lg hover:bg-accent-dark transition-transform transform hover:scale-110"
        >
          Launch Application
        </button>
      </div>
    </div>

    <!-- Loading Progress -->
    <div
      v-if="currentState === 'loading'"
      class="min-h-screen flex flex-col items-center justify-center bg-black"
    >
      <div class="w-64 h-2 bg-gray-700 rounded-full overflow-hidden">
        <div
          class="h-full bg-accent transition-all duration-300"
          :style="{ width: `${loadingProgress}%` }"
        ></div>
      </div>
      <div class="mt-4 text-lg text-secondary">{{ loadingProgress }}%</div>
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
      <video class="video-element" autoplay playsinline @ended="onVideoEnd">
        <source src="@/assets/SitangguhVideo.mp4" type="video/mp4" />
        Your browser does not support the video tag.
      </video>
    </div>

    <!-- Mission Success -->
    <div
      v-if="currentState === 'complete'"
      class="min-h-screen flex flex-col items-center justify-center bg-black text-center space-y-8"
    >
      <!-- Logo -->
      <img
        src="@/assets/digitall.png"
        alt="Digitall Logo"
        class="relative z-10 w-80"
      />
      <h2 class="text-4xl font-bold text-white">
        Application Successfully Launched
      </h2>
      <p class="text-lg text-secondary max-w-lg mx-auto">
        Thank you for joining us in this remarkable journey. Together, we move
        towards a better future for digital governance.
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { gsap } from "gsap";

// Audio files
import clickAudioFile from "@/assets/click.mp3";
import loadingAudioFile from "@/assets/loading.mp3";
import completeAudioFile from "@/assets/complete.mp3";

const clickAudio = new Audio(clickAudioFile);
const loadingAudio = new Audio(loadingAudioFile);
const completeAudio = new Audio(completeAudioFile);

const currentState = ref("initial");
const loadingProgress = ref(0);
const videoHidden = ref(false); // For controlling video transitions

const startSequence = async () => {
  // Play click sound
  clickAudio.play();

  // Delay before transitioning to loading state
  await new Promise((resolve) => setTimeout(resolve, 500));

  currentState.value = "loading";

  // Play loading sound
  loadingAudio.loop = false;
  loadingAudio.play();

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

  // Stop loading sound
  loadingAudio.pause();
  loadingAudio.currentTime = 0;

  // Transition to video after loading
  currentState.value = "video";
};

// Handle video end event with exit transition
const onVideoEnd = async () => {
  videoHidden.value = true; // Start fade-out transition

  // Play complete sound
  completeAudio.play();

  await new Promise((resolve) => setTimeout(resolve, 1000)); // Wait for fade-out to complete
  currentState.value = "complete";
};
</script>

<style scoped>
/* General Styling */
.bg-black {
  background-color: #000000;
}

.text-primary {
  color: #ffffff;
}

.text-secondary {
  color: #d1d1d1;
}

.bg-accent {
  background-color: #007bff;
}

.bg-accent-dark {
  background-color: #0056b3;
}

/* Decorative Elements */
.bg-gradient {
  background: linear-gradient(to bottom, #000000, #1a1a1a);
}

.bg-overlay {
  background: rgba(0, 0, 0, 0.75);
  position: absolute;
  inset: 0;
  z-index: 0;
}

/* Video Styling */
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

.video-element {
  width: 100vw;
  height: 100vh;
  object-fit: cover;
  display: block;
}

/* Button Styling */
button {
  text-align: center;
}
</style>
