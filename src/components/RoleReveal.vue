<script setup>
import { useGame } from '../composables/useGame.js'
import PrimaryButton from './PrimaryButton.vue'
import RoleRevealPane from './RoleRevealPane.vue'

const {
  currentPlayerName,
  currentPlayerIndex,
  revealStep,
  isCurrentImpostor,
  secretWord,
  showImpostorHint,
  hasSeenCurrentRole,
  isLastPlayer,
  nextPlayerName,
  starterName,
  markRoleSeen,
  passToNextPlayer,
  startPlaying,
  requestQuit,
} = useGame()
</script>

<template>
  <section class="mx-auto flex w-full max-w-md grow flex-col px-5 py-4">
    <div class="flex justify-end">
      <button
        type="button"
        class="flex min-h-12 items-center gap-2 rounded-2xl px-4 text-sm font-bold text-danger hover:bg-danger/10"
        aria-label="End game"
        @click="requestQuit"
      >
        End game
        <span aria-hidden="true" class="text-2xl leading-none">×</span>
      </button>
    </div>

    <div class="flex min-h-0 flex-1 flex-col justify-center py-4">
      <div v-if="revealStep === 'everyoneReady'" class="fade-up rounded-xl border-2 border-ink bg-panel p-6 text-center shadow-game">
        <p class="font-display text-3xl leading-tight text-ink">Everyone has their role!</p>
        <p class="mt-8 text-xl font-bold text-ink"><span class="text-pine">{{ starterName }}</span> will start the round.</p>
        <p class="mt-4 text-base text-muted">
          {{
            showImpostorHint
              ? 'Give the first clue, then go around the group.'
              : 'Give the first clue. The first speaker is a Keeper.'
          }}
        </p>
        <div class="mt-8">
          <PrimaryButton @click="startPlaying">Start Game</PrimaryButton>
        </div>
      </div>

      <div v-else class="relative overflow-hidden">
        <Transition name="player-slide">
          <RoleRevealPane
            :key="currentPlayerIndex"
            :player-name="currentPlayerName"
            :is-impostor="isCurrentImpostor"
            :secret-word="secretWord?.word || ''"
            :hint-word="secretWord?.hint || ''"
            :show-hint="showImpostorHint"
            @seen="markRoleSeen"
          />
        </Transition>
      </div>
    </div>

    <div v-if="revealStep !== 'everyoneReady'" class="h-14 shrink-0 overflow-anchor-none">
      <Transition name="pass-btn" :duration="{ enter: 280, leave: 0 }">
        <PrimaryButton
          v-if="hasSeenCurrentRole"
          :key="currentPlayerIndex"
          @click="passToNextPlayer"
        >
          {{ isLastPlayer ? 'Continue' : `Pass to ${nextPlayerName}` }}
        </PrimaryButton>
      </Transition>
    </div>
  </section>
</template>
