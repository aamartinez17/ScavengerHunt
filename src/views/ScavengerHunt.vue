<template>
  <div class="hunt-container container-fluid px-0">
    
    <div class="fixed-top p-3 bg-white shadow-sm" style="z-index: 1050;">
      <div class="d-flex justify-content-between align-items-center mb-1">
        <span class="small fw-bold text-muted text-uppercase">Progress</span>
        <span class="small fw-bold" style="color: var(--vday-primary)">{{ progress }}%</span>
      </div>
      <div class="progress" style="height: 8px; background-color: var(--vday-secondary);">
        <div class="progress-bar progress-bar-animated" :style="{ width: progress + '%', backgroundColor: 'var(--vday-primary)' }"></div>
      </div>
    </div>

    <div class="container px-4 mt-5 pt-5">
      <div v-if="!completed" class="riddle-card shadow-lg mt-4" data-aos="zoom-in">
        <div v-if="!unlocked">
          <h2 class="h5 mb-4 text-uppercase fw-bold" style="color: var(--vday-primary)">Memory #{{ currentStep }}</h2>
          <p class="lead mb-4">{{ activeRiddle.riddle }}</p>

          <div v-if="showHint" class="mb-4">
            <img :src="activeRiddle.blurryImage" class="img-fluid rounded-4 mb-3" style="filter: blur(8px); opacity: 0.6;" />
            <div class="alert alert-light small">💡 {{ activeRiddle.hint }}</div>
          </div>

          <input v-model="userAnswer" @keyup.enter="handleUnlock" type="text" class="custom-input text-center mb-3" placeholder="Enter secret word..."/>
          <button @click="handleUnlock" class="btn-vday w-100 py-3 mb-2">Unlock Memory</button>
          <button v-if="!showHint" @click="showHint = true" class="btn btn-link btn-sm text-muted w-100">Need a hint?</button>
          <p v-if="error" class="text-danger mt-2 small text-center">That's not the word on the note silly! Try again.</p>
        </div>

        <div v-else class="text-center" data-aos="fade-up">
          <img :src="activeRiddle.image" class="img-fluid rounded-4 shadow mb-4" />
          <h3 class="h5 mb-3" style="color: var(--vday-accent)">Memory Unlocked!</h3>
          <p class="mb-4">{{ activeRiddle.successMessage }}</p>
          <button @click="goToNext" class="btn-vday w-100 py-3">Next Clue</button>
        </div>
      </div>

      <div v-else class="riddle-card text-center shadow-lg mt-4" data-aos="fade-up">
        <h1 class="display-6 fw-bold mb-4" style="color: var(--vday-primary)">Adventure Complete</h1>
        <div class="video-container shadow rounded-4 overflow-hidden mb-4 bg-dark">
          <!-- <video controls playsinline class="w-100" poster="/images/video-thumbnail.jpg">
            <source src="/assets/adventures_2025.mp4" type="video/mp4">
          </video> -->
        </div>
        <div class="alert alert-success py-4">
          <h5 class="fw-bold">Your Prize:</h5>
          <p class="mb-0">Head to the Office. your love is waiting for you.</p>
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
  if (Cookies.get('quest_completed')) completed.value = true;
  const saved = Cookies.get('quest_progress');
  if (saved) currentStep.value = parseInt(saved);
});
</script>