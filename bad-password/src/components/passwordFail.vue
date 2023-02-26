<template>
  <div class="flex flex-col">
    <div v-if="failures.tooShort" class="">
      <p class="text-red-500 text-sm">Password must be at least 9 characters</p>
    </div>
    <div v-if="failures.tooLong" class="">
      <p class="text-red-500 text-sm">Password must be less than 10 characters</p>
    </div>
    <div v-if="failures.tooFewUppercase" class="">
      <p class="text-red-500 text-sm">Password must contain at least 1 uppercase characters</p>
    </div>
    <div v-if="failures.tooManyUppercase" class="">
      <p class="text-red-500 text-sm">Password must not contain more than 1 uppercase characters</p>
    </div>
    <div v-if="failures.tooFewSpecial" class="">
      <p class="text-red-500 text-sm">Password must contain at least 1 special characters</p>
    </div>
    <div v-if="failures.tooManySpecial" class="">
      <p class="text-red-500 text-sm">Password must not contain more than 1 special characters</p>
    </div>
    <div v-if="failures.tooFewNumbers" class="">
      <p class="text-red-500 text-sm">Password must contain at least 1 number</p>
    </div>
    <div v-if="failures.tooManyNumbers" class="">
      <p class="text-red-500 text-sm">Password must not contain more than 2 numbers</p>
    </div>
    <div v-if="failures.numberTooSmall" class="">
      <p class="text-red-500 text-sm">The first number in the password must be greater than 5</p>
    </div>

    <div v-if="failures.doesntEqualTen" class="">
      <p class="text-red-500 text-sm">The sum of the numbers in the password must equal 10</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'

const props = defineProps<{
  // failCount?: number
  password?: string
}>()

const failures = {
  tooShort: false,
  tooLong: false,
  tooFewUppercase: false,
  tooManyUppercase: false,
  tooFewSpecial: false,
  tooManySpecial: false,
  tooFewNumbers: false,
  numberTooSmall: false,
  tooManyNumbers: false,
  doesntEqualTen: false
}
watch(
  () => props.password,
  (newVal) => {
    checkPassword(newVal ? newVal : '')
  }
)
const failCount = ref(0)
const checkPassword = (password: string) => {
  const passwordLength = password.length
  const uppercaseCount = (password.match(/[A-Z]/g) || []).length
  const specialCount = (password.match(/[^A-Za-z0-9]/g) || []).length
  const numberCount = (password.match(/[0-9]/g) || []).length

  // password
  // thepassword
  // apassword
  // APassword
  // Apassword
  // Ap@ssword!
  // Ap@ssword
  // Ap@sswo123
  // Ap@sswor1
  // Ap@sswor5
  // Ap@sswo55

  if (passwordLength < 9) {
    failures.tooShort = true
    updateFailCount()
    return
  }
  if (passwordLength > 10) {
    failures.tooLong = true
    updateFailCount()
    return
  }
  if (uppercaseCount < 1) {
    failures.tooFewUppercase = true
    updateFailCount()
    return
  }
  if (uppercaseCount > 1) {
    failures.tooManyUppercase = true
    updateFailCount()
    return
  }
  if (specialCount < 1) {
    failures.tooFewSpecial = true
    updateFailCount()
    return
  }
  if (specialCount > 1) {
    failures.tooManySpecial = true
    updateFailCount()
    return
  }
  if (numberCount < 1) {
    failures.tooFewNumbers = true
    updateFailCount()
    return
  }
  if (numberCount > 2) {
    failures.tooManyNumbers = true
    updateFailCount()
    return
  }
  if (numberCount > 0 && parseInt((password.match(/[0-9]/g) || ['0'])[0]) < 5) {
    failures.numberTooSmall = true
    updateFailCount()
    return
  }
  const number1 = parseInt((password.match(/[0-9]/g) || ['0'])[0])
  const number2 = parseInt((password.match(/[0-9]/g) || ['0'])[1])
  if (number1 + number2 != 10) {
    failures.doesntEqualTen = true
    updateFailCount()
    return
  }
}

const updateFailCount = () => failCount.value++
</script>
