<template>
  <div class="hunt-container">
    <div class="content-wrapper px-0 mx-0" data-aos="fade-up">
      
      <div class="riddle-card text-center shadow-lg p-5">
        <div class="mb-4">
          <span class="badge rounded-pill px-3 py-2 mb-2" style="background-color: var(--vday-romantic); color: white;">
            February 14, 2026
          </span>
          <h1 class="display-4 fw-bold my-3" style="color: var(--vday-primary); font-family: 'Playfair Display', serif;">
            A Valentine's Adventure
          </h1>
        </div>
        
        <p class="lead text-secondary mb-5">
          {{ isReturning ? "You're doing great! Ready to find the next one?" : "A slow morning, no deadlines, and a few secrets hidden around the house. Ready to find your first clue?" }}
        </p>

        <div class="d-grid gap-3">
          <button 
            @click="startQuest" 
            class="btn-vday py-3 shadow-sm"
          >
            {{ isReturning ? "Continue the Quest" : "Let's Begin" }}
          </button>
          
          <button 
            v-if="isReturning" 
            @click="resetQuest" 
            class="btn btn-link text-muted btn-sm mt-2"
          >
            Start over from the beginning
          </button>
        </div>

        <div class="mt-4 pt-4 border-top">
          <p class="small text-muted mb-0">
            "For the one who makes every day an adventure."
          </p>
        </div>
      </div>

      <footer class="mt-4 py-2 text-center">
        <p class="text-muted mb-0" style="font-size: 0.8rem; letter-spacing: 1px;">
          BUILT WITH LOVE 2026
        </p>
      </footer>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import { useRouter } from 'vue-router';
import Cookies from 'js-cookie';
import AOS from 'aos';

const router = useRouter();
const isReturning = ref(false);
const savedStep = ref(0);

// Updated display progress based on your 16 total clues
const displayProgress = computed(() => {
  return isReturning.value ? Math.max(5, (savedStep.value / 16) * 100) : 5;
});

const startQuest = () => {
  router.push('/quest');
};

const resetQuest = () => {
  if (confirm("Are you sure you want to start the memories over?")) {
    Cookies.remove('quest_progress');
    Cookies.remove('quest_completed');
    isReturning.value = false;
    savedStep.value = 0;
  }
};

onMounted(() => {
  AOS.init({
    duration: 1000,
    once: true,
  });

  const progress = Cookies.get('quest_progress');
  const completed = Cookies.get('quest_completed');
  
  if (progress || completed) {
    isReturning.value = true;
    savedStep.value = progress ? parseInt(progress) : 16;
  }
});
</script>

<style scoped>
.hunt-container {
  background-image: linear-gradient(
    180deg, 
    rgba(255, 255, 255, 0.8) 15%, 
    rgba(255, 128, 191, 0.2) 100%
  ), 
  url('/images/background-image.png');
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  min-height: 100dvh; /* Dynamic height for mobile browsers */
  display: flex;
  flex-direction: column;
  justify-content: center; /* Vertical Center */
  align-items: center;     /* Horizontal Center */
  width: 100%;
}

.content-wrapper {
  width: 100%;
  max-width: 500px;
  padding-left: 15px; 
  padding-right: 15px;
  /* Removal of mt-auto/mb-auto allows flex-center to do the work */
}

.riddle-card {
  border-top: 6px solid var(--vday-primary);
  border-radius: 24px;
  background-color: white;
  width: 100%;
}

.btn-vday {
  background-color: var(--vday-primary);
  color: white;
  border: none;
  border-radius: 12px;
  font-size: 1.25rem;
  letter-spacing: 1px;
  text-transform: uppercase;
  font-weight: 600;
  transition: all 0.3s ease;
}

.btn-vday:active {
  transform: scale(0.98);
}

.badge {
  font-weight: 600;
  letter-spacing: 1px;
}
</style>