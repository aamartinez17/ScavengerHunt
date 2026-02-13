<template>
  <div class="hunt-container container-fluid px-4">
    <!-- <div class="progress-container fixed-top p-3">
      <div class="progress" style="height: 6px; background-color: var(--vday-secondary);">
        <div 
          class="progress-bar progress-bar-animated" 
          role="progressbar" 
          :style="{ width: displayProgress + '%', backgroundColor: 'var(--vday-primary)' }" 
        ></div>
      </div>
    </div> -->

    <div 
      class="riddle-card text-center shadow-lg p-5" 
      data-aos="fade-up"
    >
      <div class="mb-4">
        <span class="badge rounded-pill px-3 py-2 mb-2" style="background-color: var(--vday-romantic); color: white;">
          February 14, 2026
        </span>
        <h1 class="display-4 fw-bold mb-3" style="color: var(--vday-primary); font-family: 'Playfair Display', serif;">
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

    <footer class="mt-auto py-4 text-center">
      <p class="text-muted mb-0" style="font-size: 0.8rem; letter-spacing: 1px;">
        BUILT WITH LOVE 2026
      </p>
    </footer>
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

// Calculate progress for the top bar
const displayProgress = computed(() => {
  return isReturning.value ? Math.max(5, (savedStep.value / 4) * 100) : 5;
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
  // Initialize Animations
  AOS.init({
    duration: 1000,
    once: true,
  });

  // Check for existing progress
  const progress = Cookies.get('quest_progress');
  const completed = Cookies.get('quest_completed');
  
  if (progress || completed) {
    isReturning.value = true;
    savedStep.value = progress ? parseInt(progress) : 4;
  }
});
</script>

<style scoped>
.hunt-container {
  background: linear-gradient(180deg, #ffffff 0%, var(--bg-spa) 100%);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.progress-container {
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(10px);
  z-index: 1050;
}

.riddle-card {
  border-top: 6px solid var(--vday-primary);
  border-radius: 24px;
  background-color: white;
}

.btn-vday {
  font-size: 1.25rem;
  letter-spacing: 1px;
  text-transform: uppercase;
  transition: var(--transition-smooth);
}

.badge {
  font-weight: 600;
  letter-spacing: 1px;
}
</style>