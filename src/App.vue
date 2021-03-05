<template>
    <h1>memory Game</h1>

  <div class="board">
       <Card v-for="(card,index) in cardList" 
       :key="`card-${index}`" 
       :value="card.value"
       :visible="card.visible"
       :position="card.position"
       @select-card="flipCard"
        />
  </div>

</template>

<script>
import { ref } from 'vue'

import Card from './components/Card.vue'
export default {
  name: "App",
  components:{
    Card
  },
  setup(){
    // declaration part
    const cardList = ref([])

    for(let i = 0;i<16;i++){
      cardList.value.push({
        value: i,
        visible: false,
        position: i
      })
    }

    // function
    const flipCard = (payload) =>{
      cardList.value[payload.position].visible = true
    }

    // return part 

    return{
      cardList,
      flipCard
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