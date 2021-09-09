<template>
  <h1 class="title">Memory game</h1>

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
      newPlayer.value = true
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
      newPlayer.value = false
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
