<script setup lang="ts">
import { computed, watch, ref } from 'vue';
import { symbols } from './symbols';
import { shuffleCards } from './shuffleCards';
import MemoryCard from './components/MemoryCard.vue';
import BaseModal from './components/BaseModal.vue';

interface ModalData {
  index: number,
  image: string,
  name?: string
}

interface Card {
  name: string,
  image: string
}

const cards = ref<Card[]>([]);
const moves = ref(0);
const totalPairs = symbols.length;
const matchedCards = ref(new Set<number>());
const openedCards = ref(new Set<number>());
const matches = computed(() => matchedCards.value.size / 2);
const gameId = ref(0);
const isGameWon = computed(() => matches.value === totalPairs);
const isModalOpen = ref(false);
const modalData = ref<ModalData | null>(null);
const clickModalCount = ref(new Map<number, number>());

function resetGame(timeout: number) {
  gameId.value += 1;
  moves.value = 0;
  matchedCards.value.clear();
  openedCards.value.clear();

  setTimeout(() => {
    cards.value = shuffleCards([...symbols, ...symbols]);
  }, timeout);
}

function showHint(index: number, image: string) {
  const currentCount = clickModalCount.value.get(index) ?? 0;
  if (currentCount >= 2) return;
  clickModalCount.value.set(index, currentCount + 1);

  isModalOpen.value = true;
  modalData.value = { index, image };
  modalData.value.name = cards.value[index].name;
  if(!isModalOpen.value) {
    return modalData.value = null;
  }

  return modalData;
}

function openCard(index: number) {
  openedCards.value.add(index);
  if (openedCards.value.size === 2) {
    moves.value += 1;
  }
}

function getStatus(index: number) {
  if (openedCards.value.has(index)) {
    return 'opened';
  }

  if (matchedCards.value.has(index)) {
    return 'matched';
  }

  return 'closed';
}

const hasTwoCardsOpened = computed(() => openedCards.value.size === 2);
const CLOSE_TIMEOUT = 1000;

watch(hasTwoCardsOpened, (areTwoCardsOpened) => {
  if (!areTwoCardsOpened) return;

  const currentGameId = gameId.value;

  if (areTwoCardsOpened) {
    setTimeout(() => {
      if (gameId.value === currentGameId) {
        openedCards.value.clear();
      }
    }, CLOSE_TIMEOUT);
  }

  const [firstIndex, secondIndex] = [...openedCards.value.values()];
  if (cards.value[firstIndex].name === cards.value[secondIndex].name) {
    matchedCards.value.add(firstIndex);
    matchedCards.value.add(secondIndex);
  }
});

resetGame(500);
</script>

<template>
  <div id="app">
    <h1>Memory Game</h1>

    <div class="game-info">
      <div>Moves: {{ moves }}</div>
      <div>Matches: {{ matches }} / {{ totalPairs }}</div>
    </div>

    <div class="board">
      <MemoryCard
        v-for="(card, index) in cards"
        :key="index"
        :name="card.name"
        :image="card.image"
        :status="getStatus(index)"
        :disabled="hasTwoCardsOpened"
        @click="openCard(index)"
        @hint="showHint(index, $event)"
      >
      </MemoryCard>
      <BaseModal :show="isModalOpen" @close="isModalOpen = false" :data="modalData">
        <h2 class="modal-header">This card with index {{ modalData?.index }} hides <div class="modal-fruit-name">{{ modalData?.name }}</div></h2>
        <div class="modal-image-wrapper">
          <img :src="modalData?.image" :alt="modalData?.index.toString()">
        </div>
      </BaseModal>
    </div>

    <form action="#"><button @click.prevent="resetGame(500)">New Game</button></form>

    <div v-if="isGameWon" class="win-message">Congratulations! You won in {{ moves }} moves!</div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Arial', sans-serif;
  background-color: #f4f7f9;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  padding: 20px;
}

#app {
  width: 100%;
  max-width: 800px;
  text-align: center;
}

h1 {
  color: #2c3e50;
  margin-bottom: 20px;
}

.game-info {
  margin-bottom: 20px;
  display: flex;
  justify-content: space-between;
  padding: 0 10px;
}

.game-info div {
  font-size: 1.2rem;
  font-weight: bold;
  color: #34495e;
}

.board {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 15px;
  margin: 0 auto;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #2980b9;
}

.win-message {
  margin-top: 20px;
  font-size: 1.5rem;
  color: #27ae60;
  font-weight: bold;
}

.modal-header {
  margin-top: 20px;
}

.modal-image-wrapper {
  margin-top: 20px;
}

.modal-image-wrapper img {
  width: 100%;
  height: 100%;
}

.modal-fruit-name {
  display: block;
  text-align: center;
  margin-top: 5px;
}

@media (max-width: 600px) {
  .board {
    grid-template-columns: repeat(3, 1fr);
  }
  .card {
    height: 100px;
  }
}
</style>
