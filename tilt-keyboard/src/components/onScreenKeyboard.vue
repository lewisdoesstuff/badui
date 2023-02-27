<template>
  <div class="relative">
    <div class="w-full h-full bg-white dark:bg-slate-800 border dark:border-slate-500 p-4 relative z-0">
      <div class="flex flex-col w-full h-full z-10" ref="keyboardRef">
        <div v-for="(line, index) in keyboard" :key="index" class="flex flex-row w-full space-x-1.5">
          <div v-for="(key, index) in line" :key="key" ref="keyRefs">
            <div
              class="flex justify-center items-center bg-white dark:bg-slate-500 rounded-md shadow-md cursor-pointer dark:text-slate-200 hover:bg-gray-200 w-6 h-8"
            >
              {{ key }}
            </div>
            <!-- Add a line break after the last key in the line -->
            <br v-if="index === line.length - 1" />
          </div>
        </div>
      </div>
    </div>
    <rollerBall v-if="loaded" @ballPosition="keyTimer" class="" :keyboardHeight="getKeyboardHeight()" :keyboardWidth="getKeyboardWidth()" />
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import rollerBall from './rollerBall.vue';
const keyboard = [
  ['1', '2', '3', '4', '5', '6', '7', '8', '9', '0', '-', '=', '<-'],
  ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P', '[', ']', '\\'],
  ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L', ';', "'", '`'],
  ['Z', 'X', 'C', 'V', 'B', 'N', 'M', ',', '.', '/'],
];

const loaded = ref(false);
const emit = defineEmits(['keyPressed']);
const keyRefs = ref([]);
const keyboardRef = ref(null);

onMounted(() => {
  loaded.value = true;
});

const getKeyboardWidth = () => {
  if (!keyboardRef.value) return 0;
  return (keyboardRef.value as HTMLElement).getBoundingClientRect().width + 10;
};

const getKeyboardHeight = () => {
  if (!keyboardRef.value) return 0;
  return (keyboardRef.value as HTMLElement).getBoundingClientRect().height;
};

let selectedKey: string | null = null;
let timerId: ReturnType<typeof setTimeout> | null = null;

const keyTimer = (ballPosition: { x: number; y: number }) => {
  if (keyRefs.value.length > 0) {
    const key = (keyRefs.value as HTMLElement[]).find((keyRef: HTMLElement) => {
      const keyRect = keyRef.getBoundingClientRect();
      return (
        ballPosition.x > keyRect.left && ballPosition.x < keyRect.right && ballPosition.y > keyRect.top && ballPosition.y < keyRect.bottom
      );
    });

    if (key && key.innerText !== selectedKey) {
      selectedKey = key.innerText;

      if (timerId) {
        clearTimeout(timerId);
      }

      timerId = setTimeout(() => {
        if (key.innerText === selectedKey) {
          keySelected(key.innerText);
        }
        //selectedKey = null;
      }, 3000);
    } else if (!key && selectedKey) {
      selectedKey = null;
      if (timerId) {
        clearTimeout(timerId);
      }
    }
  }
};

const clearTimers = () => {
  // clear any existing timers
  const keys = keyRefs.value as HTMLElement[];
  keys.forEach((key) => {
    const timerId = key.dataset.timerId;
    if (timerId) {
      clearTimeout(parseInt(timerId));
      key.dataset.timerId = '';
    }
  });
};

const keySelected = (key: string) => {
  console.log('Key selected:', key);
  emit('keyPressed', key);
  clearTimers();
};
</script>

<style scoped>
/* Styling for the on-screen keyboard */
</style>
