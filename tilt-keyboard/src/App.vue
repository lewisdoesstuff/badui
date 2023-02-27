<template>
  <main class="flex flex-col items-center dark:bg-slate-800">
    <alertModal
      :showModal="showModal"
      :title="'Increased Security'"
      :message="'In order to increase password security, passwords must be inputted with the new roll-a-ball keyboard. Please orient your device with the screen facing up. Hover the ball over the character you wish to input for 3 seconds.'"
      @closeModal="modalClosed"
    />
    <loginBox @passwordBox="showModal = true" :password="password" />
    <onScreenKeyboard v-if="showKeyboard" @keyPressed="pressed" />
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import loginBox from './components/loginBox.vue';
import onScreenKeyboard from './components/onScreenKeyboard.vue';
import alertModal from './components/alertModal.vue';
const showModal = ref(false);
const showKeyboard = ref(false);
const password = ref('');
const modalClosed = () => {
  showModal.value = false;
  showKeyboard.value = true;
};

const pressed = (key: string) => {
  if (key === '<-') {
    password.value = password.value.slice(0, -1);
  } else {
    password.value += key;
  }
};
</script>
