<template>
  <div class="max-w-md mx-auto my-10 bg-white dark:bg-slate-900 dark:text-slate-200 rounded p-6 shadow-md">
    <h2 class="text-2xl font-bold mb-6">Login</h2>
    <form>
      <div class="mb-4">
        <label class="block text-gray-700 dark:text-slate-200 font-bold mb-2" for="username"> Username </label>
        <input
          class="appearance-none bg-whiteborder rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
          id="username"
          type="text"
          v-model="username"
          placeholder="Enter your username"
        />
      </div>
      <div class="mb-4">
        <label class="block text-gray-700 dark:text-slate-200 font-bold mb-2" for="password"> Password </label>
        <input
          class="appearance-none border rounded  bg-white w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
          id="password"
          type="password"
          :value="props.password"
          :disabled="disabled"
          placeholder="Enter your password"
          @click="passwordClicked"
        />
      </div>
      <div class="flex items-center justify-between">
        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
          type="submit"
          @click.prevent="login"
        >
          Login
        </button>
      </div>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
const username = ref('');
const disabled = ref(false);
const login = () => {
  console.log('login');
};
const emit = defineEmits(['passwordBox']);
const props = defineProps({
  password: String
})
const passwordClicked = () => {
  disabled.value = true;
  emit('passwordBox');
};


</script>
