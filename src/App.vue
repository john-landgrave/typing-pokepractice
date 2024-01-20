<script setup lang="ts">
import { ref } from 'vue';
import Pokemon from './components/Pokemon.vue';
import JSConfetti from 'js-confetti'

const pokemonNumber = ref(132);
const isCorrect = ref(false);
const confetti = new JSConfetti();

const checkAnswer = (correct: boolean) => {
  isCorrect.value = correct;
  if (correct) {
    confetti.addConfetti();
  }
}

const next = () => {
  if (isCorrect) {
    const randomNumber = new Uint8Array(1);
    crypto.getRandomValues(randomNumber);
    pokemonNumber.value = Math.floor((randomNumber[0]/256) * 152);
  }
}
</script>

<template>
  <Suspense>
    <div>
      <Pokemon class="mb-10" :key="pokemonNumber" :no="pokemonNumber" @done="checkAnswer" @next="next"></Pokemon>
      <div class="flex flex-row justify-center w-[500px]">
        <button :disabled="!isCorrect" type="button" @click="next">Next</button>
      </div>
    </div>
    <template #fallback>
      <div>
        Loading your pokemon...
      </div>
    </template>
  </Suspense>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}

button {
  @apply border rounded-lg border-gray-700;
}
button:not(:disabled) {
  @apply border rounded-lg border-black hover:text-white hover:bg-black transition-colors;
}

button:disabled {
  @apply cursor-not-allowed bg-gray-700 text-gray-500;
}
</style>
