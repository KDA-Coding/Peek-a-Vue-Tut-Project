<template>
  <h1>Peek-a-Vue</h1>
  <section class="game-board">
    <GameCard v-for="(card, index ) in cardList" 
    :key="`card-${index}`"
    :value="card" />
  </section>
</template>

<script>

import GameCard from '@/components/GameCard.vue'

export default {
  name: 'App',

  components: {
    GameCard
  },

  setup() {
    const cardList = []

    for (let i = 0; i<16; i++) {
      cardList.push(i)
    }

    return {cardList }
  }
  
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.card {
  border: 5px solid #ccc;
}

.game-board {
  justify-content: center;
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
}
</style>