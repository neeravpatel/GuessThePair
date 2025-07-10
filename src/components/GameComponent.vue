<script setup lang="ts">
import { ref, watch, onUnmounted } from "vue";
import SettingComponent from "./SettingComponent.vue";

type Difficulty = "Easy" | "Medium" | "Hard";

const puzzles: Record<
  Difficulty,
  {
    color: string;
    category: string;
    texture: string;
    answers: { name: string }[];
  }[]
> = {
  Easy: [
    {
      color: "Red",
      category: "One fruit and one vegetable",
      texture: "One is hard, one soft",
      answers: [
        {
          name: "Apple",
        },
        {
          name: "Tomato",
        },
      ],
    },
  ],
  Medium: [],
  Hard: [],
};

const difficulty = ref<Difficulty>("Easy");
const question = ref<{
  color: string;
  category: string;
  texture: string;
  answers: { name: string }[];
} | null>(null);
const showAnswer = ref(false);
const showSettings = ref(false);

const timerMinutes = ref(1); // default 1 minute
const timeLeft = ref(0);
let timerInterval = ref<number | null>(null);

function startTimer() {
  clearTimer();
  timeLeft.value = timerMinutes.value * 60;
  timerInterval.value = window.setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--;
    } else {
      showAnswer.value = true;
      clearTimer();
    }
  }, 1000);
}

function clearTimer() {
  if (timerInterval.value) {
    clearInterval(timerInterval.value);
    timerInterval.value = null;
  }
}

function resetGame() {
  showAnswer.value = false;
  loadQuestion();
}

onUnmounted(() => {
  clearTimer();
});

// Restart timer when difficulty changes
watch(difficulty, () => {
  resetGame();
});

const loadQuestion = () => {
  const pool = puzzles[difficulty.value];
  question.value = pool[Math.floor(Math.random() * pool.length)];
  showAnswer.value = false;
};

function skipTimer() {
  timeLeft.value = 0;
  showAnswer.value = true;
  clearTimer();
}

loadQuestion();
// Remove or comment out: startTimer();
</script>

<template>
  <div class="font-sans text-center mx-auto p-6">
    <div class="mb-4 flex flex-row items-center justify-between px-6">
      <button
        @click="startTimer"
        class="ml-3 px-3 py-1 bg-green-500 hover:bg-green-600 text-white rounded text-sm font-semibold transition"
        :disabled="timerInterval !== null && !showAnswer"
      >
        Start
      </button>
      <span class="font-mono text-lg dark:text-gray-200">
        ⏰
        {{
          Math.floor(timeLeft / 60)
            .toString()
            .padStart(2, "0")
        }}:{{ (timeLeft % 60).toString().padStart(2, "0") }}
      </span>
      <button
        @click="skipTimer"
        class="ml-3 px-3 py-1 bg-red-500 dark:bg-red-600 text-white rounded text-sm font-semibold transition"
        :disabled="showAnswer"
      >
        Skip
      </button>
    </div>

    <div
      class="bg-white rounded-lg shadow-md p-4 mb-4 space-y-2 dark:bg-gray-800"
    >
      <h2 class="text-xl font-semibold mb-2 dark:text-gray-300">Clues</h2>
      <div class="text-sm text-gray-600 mb-4 dark:text-gray-400">
        Use clues like color, category, and texture to guess related items.
      </div>
      <p
        class="mb-4 border-2 rounded-2xl p-1 md:p-5 dark:bg-gray-700 dark:text-gray-200"
      >
        <strong>Color:</strong> {{ question?.color }}
      </p>
      <p
        class="mb-4 border-2 rounded-2xl p-1 md:p-5 dark:bg-gray-700 dark:text-gray-200"
      >
        <strong>Category:</strong> {{ question?.category }}
      </p>
      <p
        class="mb-4 border-2 rounded-2xl p-1 md:p-5 dark:bg-gray-700 dark:text-gray-200"
      >
        <strong>Texture:</strong> {{ question?.texture }}
      </p>
    </div>

    <div
      v-if="showAnswer && question"
      class="flex justify-center gap-6 items-start mb-4"
    >
      <div
        v-for="(item, index) in question?.answers"
        :key="index"
        class="flex flex-col items-center border-2 rounded-lg p-4 bg-white dark:bg-gray-800 shadow-md dark:text-gray-200"
      >
        <p class="font-medium dark:text-gray-200">{{ item.name }}</p>
      </div>
    </div>
  </div>

  <div class="flex items-center justify-evenly">
    <button
      @click="resetGame"
      class="flex items-center justify-center bg-sky-600 hover:bg-sky-500 text-white font-semibold py-2 px-4 rounded-lg transition"
    >
      🔄 New Question
    </button>

    <button
      @click="showSettings = true"
      class="flex items-center justify-center bg-sky-600 hover:bg-sky-500 text-white font-semibold py-2 px-4 rounded-lg transition"
    >
      ⚙️ Settings
    </button>

    <SettingComponent
      v-if="showSettings"
      :difficulty="difficulty"
      :timerMinutes="timerMinutes"
      @update:difficulty="
        (val) => {
          difficulty = val;
        }
      "
      @update:timerMinutes="
        (val) => {
          timerMinutes = val;
        }
      "
      @apply="resetGame"
      @close="showSettings = false"
    />
  </div>
</template>

<style scoped></style>
