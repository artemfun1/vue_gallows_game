<template>
  <div v-show="isVisible" class="popup-container">
    <div class="popup">
      <h2 v-if="gameStatus === 'win'">Поздравляю, вы победили! 😃</h2>

      <template v-if="gameStatus === 'lose'">
        <h2>Вы проиграли. 😕</h2>
        <h3>Было имя: {{loseWord}}</h3>
      </template>

      <button @click="emit('restart')">Сыграть еще раз</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { GameStatus } from '@/types/GameStatus'
import { ref } from 'vue'

const emit = defineEmits<{
  (e: 'restart'): void
}>()

const loseWord = ref('')

const gameStatus = ref<GameStatus | null>(null)

const isVisible = ref<boolean>(false)

const open = (status: GameStatus,word?:string) => {

  if(status==='lose' && word!== undefined){
    loseWord.value = word
  }

  gameStatus.value = status

  isVisible.value = true
}

const close = () => {
  isVisible.value = false
}

defineExpose({
  open,
  close
})
</script>
