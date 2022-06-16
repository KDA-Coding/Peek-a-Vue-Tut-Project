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
  <button @click="restartGame">Restart Game</button>
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

    const restartGame = () => {
      shuffleCards()

      cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          matched: false,
          position: index,
          visible: false
        }
      })
    }

    const cardItems = [
      'bat', 
      'candy', 
      'cauldron', 
      'cupcake', 
      'ghost', 
      'moon', 
      'pumpkin', 
      'witch-hat']

    cardItems.forEach(item => {
      
      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false
      })

      cardList.value.push({
        value: item,
        visible: false,
        position: null,
        matched: false
      })


    })

    cardList.value = cardList.value.map((card, index) => {
        return {
          ...card,
          position: index

        }
    } )

    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
          if((userSelection.value[0].position === payload.position) && 
            (userSelection.value[0].faceValue === payload.faceValue) ) {
              return
            }
            else {
              userSelection.value[1] = payload
            }
            //Reduce life counter by 1 for matcher        
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

          cardList.value[cardOne.position].matched = true;
          cardList.value[cardTwo.position].matched = true;

          userSelection.value.length = 0

        }
        else {
          setTimeout(() => {
            cardList.value[cardOne.position].visible = false 
            cardList.value[cardTwo.position].visible = false 
          }, 2000)

          userSelection.value.length = 0

        }

        
      }
    }, {deep: true})

    return { cardList, userSelection, status, 
            flipCard, shuffleCards, restartGame }
  }
  
}
</script>

<style>

html, body {
  margin: 0;
  padding: 0;
}

h1 {
  margin-top: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-image: url('../public/images/page-bg.png');
  background-color: #00070c;
  height: 100vh;
  color: #fff;
  padding-top: 30px;
}

.game-board {
  justify-content: center;
  display: grid;
  grid-template-columns: repeat(4, 120px);
  grid-template-rows: repeat(4, 120px);
  grid-column-gap: 20px;
  grid-row-gap: 20px;
}
</style>