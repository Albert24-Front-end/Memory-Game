<script setup lang="ts">
import { computed, watch, ref } from 'vue';
import { symbols } from './symbols';
import { shuffleCards } from './shuffleCards';
import MemoryCard from './components/MemoryCard.vue';

const cards = ref([]);
const moves = ref(0);
const totalPairs = symbols.length;
const matchedCards = ref(new Set<number>());
const openedCards = ref(new Set<number>());
const matches = computed(() => matchedCards.value.size / 2);
const gameId = ref(0);
const isGameWon = computed(() => matches.value === totalPairs);

function resetGame() {
  gameId.value += 1;
  moves.value = 0;
  matchedCards.value.clear();
  openedCards.value.clear();

  setTimeout(() => {
    cards.value = shuffleCards([...symbols, ...symbols]);
  }, 300);
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

resetGame();
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
      />
    </div>

    <button @click="resetGame">New Game</button>

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

@media (max-width: 600px) {
  .board {
    grid-template-columns: repeat(3, 1fr);
  }
  .card {
    height: 100px;
  }
}
</style>
