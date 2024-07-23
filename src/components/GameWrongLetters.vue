<template>

<div v-if="incorrectLetters.length" class="wrong-letters-container">
    <div class="wrong-letters">
      <p>Ошибки</p>
      <span v-for="(letter,index) in incorrectLetters" :key="letter">
      {{index === incorrectLetters.length -1? letter: `${letter}, `  }}
      </span>
      
    </div>
  </div>
  </template>
  
  
  <script setup lang="ts">
type Props={
  incorrectLetters:string[]
}

defineProps<Props>()

</script>