<template>
  <h1 class="title">Memory game</h1>
  <section class="info">
    <p>welcome to Memory Card game!</p>
    <p>Powered by Vue.js 3!</p>
  </section>
  <transition-group tag="section" name="shuffle_card" class="board">
    <Card
      v-for="card in cardList"
      :key="`card-${card.value}- ${card.variant}`"
      :value="card.value"
      :visible="card.visible"
      :position="card.position"
      :matched="card.matched"
      @select-card="flipCard"
    />
  </transition-group>

  <h2>{{ status }}</h2>
  <div class="btns">
    <button v-if="newPlayer" class="button" @click="restartGame">
      <img src="/images/restart.svg" alt="" />
      Restart Game
    </button>
    <button v-else class="button" @click="startGame">
      <img src="/images/play.svg" alt="" />
      Start Game
    </button>
  </div>
</template>

<script>
import _ from 'lodash'
import { computed, ref, watch } from 'vue'
import { launchConfetti } from './utilities/confetti'
import Card from './components/Card.vue'
export default {
  name: 'App',
  components: {
    Card,
  },
  setup() {
    // declaration part
    const cardList = ref([])
    const userSelection = ref([])
    const newPlayer = ref(true)
    const startGame = () => {
      newPlayer.value = false
    }
    const status = computed(() => {
      if (remainingPairs.value === 0) {
        return 'Player wins!'
      } else {
        return `Remaining Pairs: ${remainingPairs.value}`
      }
    })

    const remainingPairs = computed(() => {
      const remainingCards = cardList.value.filter(
        (card) => card.matched === false
      ).length
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
          visible: false,
          position: index,
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
      'witch-hat',
    ]
    cardItems.forEach((item) => {
      cardList.value.push({
        value: item,
        variant: 1,
        visible: false,
        position: null,
        matched: false,
      })
      cardList.value.push({
        value: item,
        variant: 2,
        visible: false,
        position: null,
        matched: false,
      })
    })

    cardList.value = cardList.value.map((card, index) => {
      return {
        ...card,
        position: index,
      }
    })

    // function
    const flipCard = (payload) => {
      cardList.value[payload.position].visible = true

      if (userSelection.value[0]) {
        if (
          userSelection.value[0].position === payload.position &&
          userSelection.value[0].faceValue === payload.faceValue
        ) {
          return
        } else {
          userSelection.value[1] = payload
        }
      } else {
        userSelection.value[0] = payload
      }
    }
    watch(remainingPairs, (currentValue) => {
      if (currentValue === 0) {
        launchConfetti()
      }
    })
    watch(
      userSelection,
      (currentValue) => {
        if (currentValue.length === 2) {
          const cardOne = currentValue[0]
          const cardTwo = currentValue[1]
          if (cardOne.faceValue === cardTwo.faceValue) {
            cardList.value[cardOne.position].matched = true
            cardList.value[cardTwo.position].matched = true
          } else {
            setTimeout(() => {
              cardList.value[cardOne.position].visible = false
              cardList.value[cardTwo.position].visible = false
            }, 2000)
          }

          userSelection.value.length = 0
        }
      },
      { deep: true }
    )

    // return part

    return {
      cardList,
      flipCard,
      userSelection,
      status,
      remainingPairs,
      shuffleCards,
      restartGame,
      startGame,
      newPlayer,
    }
  },
}
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  font-family: 'Roboto', sans-serif;
}
#app {
  font-family: sans-serif;
  color: #2c3e50;
  text-align: center;
  background: #0f2027;
  background: -webkit-linear-gradient(to right, #0f2027, #203a43, #2c5364);
  background: linear-gradient(to right, #0f2027, #203a43, #2c5364);
  background-size: cover;
  color: #333;
  padding-top: 10px;
  min-height: 100vh;
}
h1 {
  font-family: 'Lato', sans-serif;
  font-size: 30px;
  padding-block: 30px;
  text-align: center;
  text-transform: uppercase;
  text-rendering: optimizeLegibility;
}
h1.title {
  color: #202020;
  letter-spacing: 0.1em;
  text-shadow: -1px -1px 1px #111, 2px 2px 1px #363636;
}
h2 {
  color: #fff;
  font-family: 'Lato', sans-serif;
}
.board {
  display: grid;
  grid-template-columns: repeat(4, 100px);
  grid-template-rows: repeat(4, 100px);
  grid-gap: 30px;
  justify-content: center;
  padding-bottom: 30px;
}

.button {
  background-color: lightsalmon;
  border: none;
  outline: none;
  padding: 0.4rem 1rem;
  font-size: 1rem;
  font-weight: 600;
  color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0 auto;
  margin-top: 10px;
  border-radius: 10px;
  transition: 0.5s ease-in-out;
  cursor: pointer;
}
.button img {
  padding-right: 5px;
}
.shuffle_card-move {
  transition: transform 0.8s ease-in;
}
.info p {
  margin: 0;
  font-family: 'Lato', sans-serif;
  color: #fff;
  font-size: 1rem;
  font-weight: 600;
}
.info p:last-child {
  margin-bottom: 30px;
}
</style>
