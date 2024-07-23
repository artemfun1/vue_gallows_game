<script setup lang="ts">
import axios from 'axios'
import { computed, ref, watch } from 'vue'
import GameFigure from './components/GameFigure.vue'
import GameHeader from './components/GameHeader.vue'
import GameNotification from './components/GameNotification.vue'
import GamePopup from './components/GamePopup.vue'
import GameWord from './components/GameWord.vue'
import GameWrongLetters from './components/GameWrongLetters.vue'

const getRandomName = async () => {
  try {
    const { data } = await axios.get<{ FirstName: string }>(
      'https://api.randomdatatools.ru/?unescaped=false&typeName=classic&params=FirstName'
    )
    console.log(data.FirstName)
    word.value = data.FirstName.toLowerCase()
  } catch (error) {
    console.log(error)
    word.value = ''
  }
}

getRandomName()

const word = ref('')
const letters = ref<string[]>([])

const correctLetters = computed(() => letters.value.filter((x) => word.value.includes(x)))

const incorrectLetters = computed(() => letters.value.filter((x) => !word.value.includes(x)))

const isLose = computed(() => incorrectLetters.value.length === 6)

const isWin = computed(() => [...word.value].every((x) => correctLetters.value.includes(x)))

const notification = ref<InstanceType<typeof GameNotification> | null>(null)
const popup = ref<InstanceType<typeof GamePopup> | null>(null)

watch(incorrectLetters, () => {
  if (isLose.value) {
    popup.value?.open('lose', word.value)
  }
})

watch(correctLetters, () => {
  if (isWin.value) {
    popup.value?.open('win')
  }
})

window.addEventListener('keydown', ({ key }) => {
  if (isLose.value || isWin.value) {
    return
  }

  if (letters.value.includes(key)) {
    notification.value?.open()

    setTimeout(() => {
      notification.value?.close()
    }, 1500)
    return
  }

  if (/[а-яА-ЯёЁ]/.test(key)) {
    letters.value.push(key.toLowerCase())
  }
})

const restart = async () => {
  await getRandomName()
  letters.value = []
  popup.value?.close()
}
</script>

<template>
  <game-header />

  <div class="game-container">
    <game-figure :wrong-length="incorrectLetters.length" />

    <game-wrong-letters :word="word" :incorrect-letters="incorrectLetters" />

    <game-word :word="word" :correct-letters="correctLetters" />
  </div>

  <game-popup @restart="restart" ref="popup" />

  <game-notification ref="notification" />
</template>

<style scoped></style>
