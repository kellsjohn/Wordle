<template lang="pug">
div(class='w-full h-screen flex flex-col justify-center items-center bg-black text-white')
  div(class='mb-4 grid grid-rows-6 gap-2')
    div(v-for='(guess, attempt) in guesses' :key='attempt' class='grid grid-cols-5 gap-2')
      div(v-for='(letter, index) in guess' :key='index' :style='{ transitionDelay: `${0.15 + 0.3 * (index - 1)}s`, animationDelay: `${0.3 * (index - 1)}s` }', class='w-12 h-12 flex items-center justify-center border-2 border-gray-500 text-xl font-bold uppercase' :class='[colors[attempt][index], {"flip-animation": colors[attempt][index]}]') {{ letter }}
  
  div(v-for='(keys, rowIndex) in keyboard' class='p-1 flex space-x-2')
    div(v-for='(letter, columnIndex) in keys' class='px-4 py-2 rounded-md bg-gray-900 cursor-pointer hover:bg-yellow-500 hover:text-black' @click='addLetter(letter)') {{ letter }}
</template>

<script setup>
import { ref } from "vue";

const words = ["apple", "grape", "melon", "lemon", "peach"];
const keyboard = [
  ["Q", "W", "E", "R", "T", "Y", "U", "I", "O", "P"],
  ["A", "S", "D", "F", "G", "H", "J", "K", "L"],
  ["Enter", "Z", "X", "C", "V", "B", "N", "M", "Back"],
];
const guesses = ref(
  Array(6)
    .fill("")
    .map(() => Array(5).fill(""))
);
const colors = ref(
  Array(6)
    .fill("")
    .map(() => Array(5).fill(""))
);
const targetWord = ref(words[Math.floor(Math.random() * words.length)]);
console.log("targetWord", targetWord.value);
const keyboardRowIndex = ref(0);
const keyboardColumnIndex = ref(0);
const isGameOver = ref(false);
const currentAttempt = ref(0);
const results = ref([]);

function addLetter(letter) {
  if (letter === "Enter") {
    submitGuess();
  } else if (letter === "Back") {
    removeLetter();
  } else if (!isGameOver.value) {
    const currentRow = guesses.value[currentAttempt.value];
    const firstEmptyIndex = currentRow.findIndex((letter) => letter === "");

    if (firstEmptyIndex !== -1) {
      guesses.value[currentAttempt.value][firstEmptyIndex] = letter;
    }
  }
}

function removeLetter() {
  if (isGameOver.value) {
    return;
  }
  const currentRow = guesses.value[currentAttempt.value];
  for (let i = currentRow.length - 1; i >= 0; i--) {
    if (currentRow[i] !== "") {
      currentRow[i] = "";
      break;
    }
  }
}

function submitGuess() {
  if (isGameOver.value) {
    return;
  }
  const currentRow = guesses.value[currentAttempt.value];
  const guess = currentRow.join("").toLowerCase();

  if (!guess) {
    alert("Please enter a 5 letter word!");
    return;
  }

  if (currentRow.includes("")) {
    alert("Not enough letters");
    return;
  }

  if (!words.includes(guess)) {
    alert("Invalid word!");
    return;
  }

  // Apply colors for the current row
  for (let i = 0; i < 5; i++) {
    const letter = currentRow[i].toLowerCase();
    if (letter === targetWord.value[i]) {
      colors.value[currentAttempt.value][i] = "bg-green-500 border-green-500";
    } else if (targetWord.value.includes(letter)) {
      colors.value[currentAttempt.value][i] = "bg-yellow-500 border-yellow-500";
    } else {
      colors.value[currentAttempt.value][i] = "bg-gray-700 border-gray-700";
    }
  }

  // Check if the guess is correct
  if (guess === targetWord.value) {
    isGameOver.value = true;
    alert("Congratulations! You guessed the word correctly.");
    return;
  }

  // If the guess is incorrect, move to the next attempt
  if (currentAttempt.value < 5) {
    currentAttempt.value += 1;
  } else {
    alert(`Game Over! The word was ${targetWord.value}`);
    isGameOver.value = true;
  }
}
</script>

<style scoped>
@keyframes flip {
  0% {
    transform: rotateX(0deg);
  }
  50% {
    transform: rotateX(180deg);
  }
  100% {
    transform: rotateX(0deg);
  }
}

.flip-animation {
  transform-origin: center;
  animation: flip 0.6s ease-in-out forwards;
  backface-visibility: hidden;
}
</style>
