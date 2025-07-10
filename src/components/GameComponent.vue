<script setup lang="ts">
import { ref } from "vue";

type Difficulty = "Easy" | "Medium" | "Hard";

const puzzles: Record<
  Difficulty,
  {
    color: string;
    category: string;
    texture: string;
    answers: { name: string; image: string }[];
  }[]
> = {
  Easy: [
    {
      color: "Red",
      category: "One fruit and one vegetable",
      texture: "One is hard, one soft",
      answers: [
        { name: "Apple", image: "https://i.imgur.com/NzI5V3L.jpg" },
        { name: "Tomato", image: "https://i.imgur.com/M2xKp8T.jpg" },
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
  answers: { name: string; image: string }[];
} | null>(null);
const showAnswer = ref(false);

const loadQuestion = () => {
  const pool = puzzles[difficulty.value];
  question.value = pool[Math.floor(Math.random() * pool.length)];
  showAnswer.value = false;
};

loadQuestion();
</script>

<template>
  <div class="font-sans text-center mx-auto p-6">
    <div class="mb-4">
      <label class="mr-2 font-medium dark:text-gray-300"
        >Select Difficulty:</label
      >
      <div class="inline-flex gap-4">
        <label class="inline-flex items-center">
          <input
            type="radio"
            value="Easy"
            v-model="difficulty"
            @change="loadQuestion"
            class="form-radio text-blue-600"
          />
          <span class="ml-1 dark:text-gray-200">Easy</span>
        </label>
        <label class="inline-flex items-center">
          <input
            type="radio"
            value="Medium"
            v-model="difficulty"
            @change="loadQuestion"
            class="form-radio text-blue-600"
          />
          <span class="ml-1 dark:text-gray-200">Medium</span>
        </label>
        <label class="inline-flex items-center">
          <input
            type="radio"
            value="Hard"
            v-model="difficulty"
            @change="loadQuestion"
            class="form-radio text-blue-600"
          />
          <span class="ml-1 dark:text-gray-200">Hard</span>
        </label>
      </div>
    </div>

    <div class="bg-white rounded-lg shadow-md p-4 mb-4 space-y-2 dark:bg-gray-800">
      <h2 class="text-xl font-semibold mb-2 dark:text-gray-300">Clues</h2>
      <div class="text-sm text-gray-600 mb-4 dark:text-gray-400">
        Use clues like color, category, and texture to guess related items.
      </div>
      <p class="mb-4 border-2 rounded-2xl p-1 md:p-5 dark:bg-gray-700 dark:text-gray-200">
        <strong>Color:</strong> {{ question?.color }}
      </p>
      <p class="mb-4 border-2 rounded-2xl p-1 md:p-5 dark:bg-gray-700 dark:text-gray-200">
        <strong>Category:</strong> {{ question?.category }}
      </p>
      <p class="mb-4 border-2 rounded-2xl p-1 md:p-5 dark:bg-gray-700 dark:text-gray-200">
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
        class="flex flex-col items-center"
      >
        <img
          :src="item.image"
          :alt="item.name"
          class="w-24 h-24 object-cover rounded-lg mb-2 shadow"
        />
        <p class="font-medium dark:text-gray-200">{{ item.name }}</p>
      </div>
    </div>
  </div>

  <div class="flex items-center justify-between text-center mx-auto px-6">
    <button
      @click="showAnswer = !showAnswer"
      class="mr-2 flex items-center justify-center bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 transition"
    >
      {{ showAnswer ? "Hide Answer" : "Show Answer" }}
    </button>

    <button
      @click="loadQuestion"
      class="flex items-center justify-center bg-sky-600 hover:bg-sky-500 text-white font-semibold py-2 px-4 rounded-lg transition"
    >
      🔄 New Question
    </button>
  </div>
</template>

<style scoped></style>
