<script setup>
import { onMounted, ref } from 'vue'
import SecretSwipeCard from './SecretSwipeCard.vue'

const props = defineProps({
  playerName: {
    type: String,
    required: true,
  },
  isImpostor: {
    type: Boolean,
    default: false,
  },
  secretWord: {
    type: String,
    default: '',
  },
  hintWord: {
    type: String,
    default: '',
  },
  showHint: {
    type: Boolean,
    default: true,
  },
})

const emit = defineEmits(['seen'])

const headingRef = ref(null)
const isCardOpen = ref(false)
const hasSeen = ref(false)

const playerName = props.playerName
const isImpostor = props.isImpostor
const secretWord = props.secretWord
const hintWord = props.hintWord
const showHint = props.showHint

function onRoleSeen() {
  isCardOpen.value = true
  hasSeen.value = true
  emit('seen')
}

onMounted(() => {
  headingRef.value?.focus({ preventScroll: true })
})
</script>

<template>
  <div class="text-center">
    <p class="eyebrow text-pine">Pass the phone to</p>
    <h1 ref="headingRef" tabindex="-1" class="mt-3 font-archivo text-4xl tracking-wide text-ink">
      {{ playerName }}
    </h1>

    <div class="mt-6">
      <SecretSwipeCard
        :player-name="playerName"
        :is-impostor="isImpostor"
        :secret-word="secretWord"
        :hint-word="hintWord"
        :show-hint="showHint"
        @seen="onRoleSeen"
        @closed="isCardOpen = false"
      />
    </div>

    <p class="mt-4 flex min-h-10 items-center justify-center text-sm text-muted" aria-live="polite">
      <template v-if="isCardOpen">Memorize it! The card hides itself in a moment.</template>
      <template v-else-if="!hasSeen">Swipe up to see your role.</template>
      <template v-else>Pass the phone when you're ready.</template>
    </p>
  </div>
</template>
