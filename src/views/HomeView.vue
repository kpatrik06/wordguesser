<template>
  <div class="min-h-screen bg-gray-100 flex flex-col items-center justify-center p-4">
    <h1 class="text-3xl font-bold mb-4">Word Guesser</h1>

    <!-- Lives Display -->
    <div class="flex mb-4">
      <svg
        v-for="n in 5"
        :key="n"
        xmlns="http://www.w3.org/2000/svg"
        :fill="n <= lives ? 'red' : 'none'"
        viewBox="0 0 24 24"
        stroke="red"
        class="w-6 h-6 mx-1"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M12 21.35l-1.45-1.32C5.4 15.36 2 12.28 2 8.5 2 5.42 
             4.42 3 7.5 3c1.74 0 3.41 0.81 4.5 2.09C13.09 3.81 
             14.76 3 16.5 3 19.58 3 22 5.42 22 8.5c0 3.78-3.4 
             6.86-8.55 11.54L12 21.35z"
        />
      </svg>
    </div>

    <div class="mb-6">
      <p class="text-xl tracking-widest font-mono">
        <span
          v-for="(letter, index) in originalWord.split('')"
          :key="index"
          class="mx-1 px-2 py-1 rounded"
          :class="{
            'text-green-600': guessedLetters.includes(letter),
            'bg-red-300 text-white': wrongLetters.includes(letter),
            'border-b border-gray-500': !guessedLetters.includes(letter) && !wrongLetters.includes(letter)
          }"
        >
          {{ guessedLetters.includes(letter) ? letter : '_' }}
        </span>
      </p>
    </div>

    <!-- Virtual Keyboard -->
    <div class="grid grid-cols-9 gap-2 mb-4">
      <button
        v-for="letter in alphabet"
        :key="letter"
        :disabled="guessedLetters.includes(letter) || wrongLetters.includes(letter) || lives <= 0"
        @click="handleKeyboardClick(letter)"
        class="px-3 py-2 rounded text-black transition-colors duration-150"
        :class="{
          'bg-green-300': guessedLetters.includes(letter),
          'bg-red-300': wrongLetters.includes(letter),
          'bg-gray-300 hover:bg-gray-400': !guessedLetters.includes(letter) && !wrongLetters.includes(letter)
        }"
      >
        {{ letter }}
      </button>
    </div>

    <div class="mt-4 text-red-500" v-if="message">
      {{ message }}
    </div>

    <div class="mt-6">
      <button
        @click="resetGame"
        class="bg-gray-900 text-white px-6 py-2 rounded-full shadow-md hover:scale-105 hover:bg-gray-700 transition-transform duration-200"
      >
        🔄New Game
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const wordList = ['VUE', 'TAILWIND', 'GUESS', 'JAVASCRIPT', 'COMPUTER']
const originalWord = ref(randomWord())
const guessedLetters = ref([])
const wrongLetters = ref([])
const message = ref('')
const lives = ref(5)
const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('')

function randomWord() {
  return wordList[Math.floor(Math.random() * wordList.length)]
}

function handleKeyboardClick(letter) {
  if (guessedLetters.value.includes(letter) || wrongLetters.value.includes(letter)) return

  if (originalWord.value.includes(letter)) {
    guessedLetters.value.push(letter)
    message.value = ''
  } else {
    wrongLetters.value.push(letter)
    lives.value--
    message.value = `Wrong guess. You have ${lives.value} lives left.`
  }

  if (lives.value <= 0) {
    message.value = `Game over! The word was: ${originalWord.value}`
  }

  if (originalWord.value.split('').every(letter => guessedLetters.value.includes(letter))) {
    message.value = 'Congratulations! You guessed the word!'
  }
}

function resetGame() {
  originalWord.value = randomWord()
  guessedLetters.value = []
  wrongLetters.value = []
  message.value = ''
  lives.value = 5
}
</script>

<style scoped>
body {
  font-family: 'Inter', sans-serif;
}
</style>
