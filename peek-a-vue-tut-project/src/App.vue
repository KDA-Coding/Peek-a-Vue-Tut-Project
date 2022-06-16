<template>
  <h1>Peek-a-Vue</h1>
  <section class="game-board">
    <GameCard v-for="(card, index ) in cardList" 
    :key="`card-${index}`"
    :value="card.value"
    :matched="card.matched"
    :position="card.position"
    :visible="card.visible"
    @select-card="flipCard" />
  </section>
  <h2>{{ status }}</h2>
  <button @click="shuffleCards">Shuffle Cards</button>
</template>

<script>

import _ from 'lodash'
import GameCard from '@/components/GameCard.vue'
import { ref, watch, computed } from 'vue'

export default {
  name: 'App',

  components: {
    GameCard
  },

  setup() {
    const cardList = ref([])
    const userSelection = ref([])
    
    const status = computed (() => {
      if (remainingPairs.value === 0) {
        return "Player wins!"
      }
      else {
        return `Remaining Pairs ${remainingPairs.value}`
      }
    })

    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter
      (card => card.matched === false).length

      return remainingCards / 2
    })

    const shuffleCards = () => {
      cardList.value = _.shuffle(cardList.value)
    }

    for (let i = 0; i<16; i++) {
      cardList.value.push({
        value: i,
        visible: true,
        position: i,
        matched: false
      })
    }

    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
        userSelection.value[1] = payload
      }
      else {
        userSelection.value[0] = payload
      }
    }

    watch(userSelection, currentValue => {
      if(currentValue.length === 2) {

        const cardOne = currentValue[0]
        const cardTwo = currentValue[1]

        if(cardOne.faceValue === cardTwo.faceValue) {
          status.value = 'Matched!'

          cardList.value[cardOne.position].matched = true;
          cardList.value[cardTwo.position].matched = true;

          userSelection.value.length = 0

        }
        else {
          status.value = 'Mismatched.'

          cardList.value[cardOne.position].visible = false 
          cardList.value[cardTwo.position].visible = false 

          userSelection.value.length = 0
        }

        
      }
    }, {deep: true})

    return { cardList, flipCard, userSelection, status, shuffleCards }
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

.game-board {
  justify-content: center;
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-column-gap: 30px;
  grid-row-gap: 30px;
}
</style>