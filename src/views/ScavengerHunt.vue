<template>
  <div class="hunt-container">
    
    <div class="fixed-top p-3 bg-white shadow-sm" style="z-index: 1050;">
      <div class="d-flex justify-content-between align-items-center mb-1">
        <span class="small fw-bold text-muted text-uppercase">Progress</span>
        <span class="small fw-bold" style="color: var(--vday-accent)">{{ progress }}%</span>
      </div>
      <div class="progress" style="height: 8px; background-color: var(--vday-romantic);">
        <div class="progress-bar progress-bar-animated" :style="{ width: progress + '%', backgroundColor: 'var(--vday-accent)' }"></div>
      </div>
    </div>

    <div class="content-wrapper px-0 mx-0">
      <div v-if="!completed" class="riddle-card shadow-lg" data-aos="zoom-in">
        
        <div v-if="!unlocked">
          <h2 class="h5 mb-4 text-uppercase fw-bold" style="color: var(--vday-primary)">
            Memory #{{ currentStep + 1 }}
          </h2>
          <p class="lead mb-4">{{ activeRiddle.riddle }}</p>

          <div v-if="showHint" class="mb-4">
            <img :src="activeRiddle.blurryImage" class="blur-effect mb-3" />
            <div class="alert alert-light small py-2">💡 {{ activeRiddle.hint }}</div>
          </div>

          <div class="d-grid gap-2">
            <input 
              v-model="userAnswer" 
              @keyup.enter="handleUnlock" 
              type="text" 
              class="custom-input text-center mb-2" 
              placeholder="Enter secret word..."
            />
            <button @click="handleUnlock" class="btn-vday w-100 py-3">Unlock Memory</button>
            <button v-if="!showHint" @click="showHint = true" class="btn btn-link btn-sm text-muted">Need a hint?</button>
          </div>
          
          <p v-if="error" class="text-danger mt-3 small">That's not the word on the note silly! Try again.</p>
        </div>

        <div v-else class="text-center" data-aos="fade-up">
          <img :src="activeRiddle.image" class="image-reveal mb-4" />
          <h3 class="h5 mb-3" style="color: var(--vday-accent)">Memory Unlocked!</h3>
          <p class="mb-4">{{ activeRiddle.successMessage }}</p>
          <button @click="goToNext" class="btn-vday w-100 py-3">Next Memory</button>
        </div>
      </div>

      <div v-else class="riddle-card text-center shadow-lg" data-aos="fade-up">
        <h1 class="display-6 fw-bold mb-4" style="color: var(--vday-primary)">Adventure Complete</h1>
        <div class="video-container shadow rounded-4 overflow-hidden mb-4 bg-dark">
          <!-- <video controls playsinline class="w-100" poster="/images/video-thumbnail.jpg">
            <source src="/assets/adventures_2025.mp4" type="video/mp4">
          </video> -->
        </div>
        <div class="alert alert-success py-4">
          <h5 class="fw-bold">Your Prize:</h5>
          <p class="mb-0">Head to the Office. Your love is waiting for you.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import Cookies from 'js-cookie';
import AOS from 'aos';
import { questSteps } from '../assets/QuestData';

const currentStep = ref(0);
const userAnswer = ref('');
const unlocked = ref(false);
const showHint = ref(false);
const error = ref(false);
const completed = ref(false);

const activeRiddle = computed(() => questSteps[currentStep.value]);
const progress = computed(() => completed.value ? 100 : Math.round((currentStep.value / questSteps.length) * 100));

const handleUnlock = () => {
  if (userAnswer.value.toLowerCase().trim() === activeRiddle.value.answer.toLowerCase()) {
    unlocked.value = true;
    error.value = false;
  } else {
    error.value = true;
  }
};

const goToNext = () => {
  if (currentStep.value < questSteps.length - 1) {
    currentStep.value++;
    userAnswer.value = '';
    unlocked.value = false;
    showHint.value = false;
    Cookies.set('quest_progress', currentStep.value);
  } else {
    completed.value = true;
    Cookies.set('quest_completed', 'true');
  }
};

onMounted(() => {
  AOS.init();
  if (Cookies.get('quest_completed') === 'true') completed.value = true;
  const saved = Cookies.get('quest_progress');
  if (saved) currentStep.value = parseInt(saved);
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
  min-height: 100dvh;
  display: flex;
  justify-content: center; /* Vertical Center */
  align-items: center;     /* Horizontal Center */
  padding-top: 80px;      /* Space for fixed header */
  padding-bottom: 40px;
  
}

.content-wrapper {
  width: 100%;
  max-width: 450px;       /* Tightened for better mobile feel */
  margin: 0 auto;
  
}

.riddle-card {
  background: white;
  border-radius: 24px;
  padding: 2.5rem 1.5rem;
  border-top: 6px solid var(--vday-primary);
  text-align: center;
  width: 100%;
}

.blur-effect {
  filter: blur(4px);
  opacity: 0.7;
  max-height: 200px;
  width: 100%;
  object-fit: cover;
  border-radius: 16px;
}

.image-reveal {
  width: 100%;
  border-radius: 16px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
  max-height: 300px;
  object-fit: cover;
}

.custom-input {
  border: 2px solid var(--vday-secondary);
  border-radius: 12px;
  padding: 12px;
  width: 100%;
  font-size: 16px; 
}

.btn-vday {
  background-color: var(--vday-primary);
  color: white;
  border: none;
  border-radius: 12px;
  font-weight: 600;
  transition: transform 0.2s;
}

.btn-vday:active {
  transform: scale(0.98);
}
</style>