<template>
    <h1>memory Game</h1>

  <section class="board">
       <Card v-for="(card,index) in cardList" 
       :key="`card-${index}`" 
       :value="card.value"
       :visible="card.visible"
       :position="card.position"
       :matched="card.matched"
       @select-card="flipCard"
        />
  </section>
  <!-- <h2>{{ userSelection }}</h2> -->
  <p>{{status}}</p>

</template>

<script>
import { ref, watch } from 'vue'

import Card from './components/Card.vue'
export default {
  name: "App",
  components:{
    Card
  },
  setup(){
    // declaration part
    const cardList = ref([])
    const userSelection = ref([])
    const status =ref('')
    for(let i = 0;i<16;i++){
      cardList.value.push({
        value: i,
        visible: false,
        position: i,
        matched: false
      })
    }

    // function
    const flipCard = (payload) =>{
      cardList.value[payload.position].visible = true

      if(userSelection.value[0]){
        userSelection.value[1] = payload
      }else{
         userSelection.value[0] = payload
      }
    }

    watch(userSelection,currentValue => {
      if(currentValue.length === 2){
      const cardOne = currentValue[0]
      const cardTwo = currentValue[1]
      if(cardOne.faceValue === cardTwo.faceValue){
        status.value = "Matched!"
        cardList.value[cardOne.position].matched = true
        cardList.value[cardTwo.position].matched = true
      }else{
        status.value = "Mismatch!"
        cardList.value[cardOne.position].visible = false
        cardList.value[cardTwo.position].visible = false
      }
     
        userSelection.value.length = 0
      }
    },{deep: true})

    // return part 

    return{
      cardList,
      flipCard,
      userSelection,
      status
    }
  }

}
</script>

<style>
#app{
  text-align: center;
}

.board{
  display: grid;
  grid-template-columns: 100px 100px 100px 100px;
  grid-template-rows: 100px 100px 100px 100px;
  grid-gap: 30px;
  justify-content: center;

}
</style>