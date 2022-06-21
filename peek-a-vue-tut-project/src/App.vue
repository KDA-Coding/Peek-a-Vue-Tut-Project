<template>
  <h1 class="sr-only">Peek-a-Vue</h1>
  <img src="../public/images/peek-a-vue-title.png" class="title"/>
  <section class="description">
    <p>Welcome to Peek-a-Vue!</p>
    <p>A card matching game powered by Vue.js 3!</p>
  </section>
  <transition-group tag="section" class="game-board" name="shuffle-card">
    <GameCard v-for="card in cardList" 
    :key="`${card.value}-${card.variant}`"
    :value="card.value"
    :matched="card.matched"
    :position="card.position"
    :visible="card.visible"
    @select-card="flipCard" />
  </transition-group>
  <h2 class="status">{{ status }}</h2>
  <button v-if="newPlayer" @click="startGame" class="button">
  <img src="/images/play.svg"
  alt="Start Icon"/>
  Start Game </button>
  <button v-else @click="restartGame" class="button">
  <img src="/images/restart.svg"
  alt="Restart Icon"/>
  Restart Game </button>
</template>

<script>

import _ from 'lodash'
import GameCard from '@/components/GameCard.vue'
import { ref, watch, computed } from 'vue'
import { launchConfetti } from './utilities/confetti'

export default {
  name: 'App',

  components: {
    GameCard
  },

  setup() {
    const cardList = ref([])
    const userSelection = ref([])
    const newPlayer = ref(true)

    const startGame = () => {
      newPlayer.value = false;

      restartGame()
    }

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

    const restartGame = () => {
      cardList.value = _.shuffle(cardList.value)

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
        variant: 1,
        visible: false,
        position: null,
        matched: false
      })

      cardList.value.push({
        value: item,
        variant: 2,
        visible: true,
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

    watch(remainingPairs, currentValue => {

      if(currentValue === 0) {
        launchConfetti()
      }

    } )

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

    return { cardList, userSelection, status, newPlayer,
            startGame, flipCard, restartGame }
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
  padding-top: 15px;
}

.description {
  font-family: 'Titillium-Web', sans-serif;
}

.description p {
  margin: 0;
  font-size: 1.2rem;
}

.description p:last-child {
  margin-bottom: 15px;
}

.status {
  font-family: 'Titillium-Web', sans-serif;
}

.button {
  background-color: orange;
  color: white;
  padding: 0.75rem 0.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 0 auto;
  font-weight: bold;
  font-family: 'Titillium-Web', sans-serif;
  font-size: 1.1rem;
  border: 0;
  border-radius: 10px;
}

.button img {
  padding-right: 5px;
}

.game-board {
  justify-content: center;
  display: grid;
  grid-template-columns: repeat(4, 100px);
  grid-template-rows: repeat(4, 100px);
  grid-column-gap: 20px;
  grid-row-gap: 20px;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0,0,0,0);
  border: 0;
}

.title {
  padding-bottom: 10px;
}

.shuffle-card-move {

  transition: transform 0.8s ease-in;

}

</style>