<script setup lang="ts">
import { ref, watch, toRefs } from "vue";

const props = defineProps<{
  difficulty: string;
  timerMinutes: number;
}>();
const emit = defineEmits([
  "update:difficulty",
  "update:timerMinutes",
  "close",
  "apply",
]);

const localDifficulty = ref(props.difficulty);
const localTimerMinutes = ref(props.timerMinutes);

watch(
  () => props.difficulty,
  (val) => (localDifficulty.value = val)
);
watch(
  () => props.timerMinutes,
  (val) => (localTimerMinutes.value = val)
);

function applySettings() {
  emit("update:difficulty", localDifficulty.value);
  emit("update:timerMinutes", localTimerMinutes.value);
  emit("apply");
  emit("close");
}
</script>

<template>
  <div
    class="fixed inset-0 bg-black bg-opacity-40 flex items-center justify-center z-50"
  >
    <div
      class="bg-white dark:bg-gray-800 rounded-lg shadow-lg p-6 min-w-[320px]"
    >
      <h2 class="text-lg font-semibold mb-4 dark:text-gray-200">Settings</h2>
      <div class="mb-4">
        <label class="mr-2 font-medium dark:text-gray-300"
          >Select Difficulty:</label
        >
        <div class="inline-flex gap-4">
          <label class="inline-flex items-center">
            <input
              type="radio"
              value="Easy"
              v-model="localDifficulty"
              class="form-radio text-blue-600"
            />
            <span class="ml-1 dark:text-gray-200">Easy</span>
          </label>
          <label class="inline-flex items-center">
            <input
              type="radio"
              value="Medium"
              v-model="localDifficulty"
              class="form-radio text-blue-600"
            />
            <span class="ml-1 dark:text-gray-200">Medium</span>
          </label>
          <label class="inline-flex items-center">
            <input
              type="radio"
              value="Hard"
              v-model="localDifficulty"
              class="form-radio text-blue-600"
            />
            <span class="ml-1 dark:text-gray-200">Hard</span>
          </label>
        </div>
      </div>
      <div class="mb-4">
        <label class="mr-2 font-medium dark:text-gray-300"
          >Timer (minutes):</label
        >
        <input
          type="number"
          min="1"
          v-model.number="localTimerMinutes"
          class="w-16 border rounded px-2 py-1 dark:bg-gray-800 dark:text-gray-200"
        />
      </div>
      <div class="flex justify-end gap-2">
        <button
          @click="$emit('close')"
          class="px-4 py-2 rounded bg-gray-300 dark:bg-gray-700 text-gray-800 dark:text-gray-200 hover:bg-gray-400 dark:hover:bg-gray-600"
        >
          Close
        </button>
        <button
          @click="applySettings"
          class="px-4 py-2 rounded bg-blue-600 text-white hover:bg-blue-700"
        >
          Apply
        </button>
      </div>
    </div>
  </div>
</template>
