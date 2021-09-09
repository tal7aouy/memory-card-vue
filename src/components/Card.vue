<template>
  <div class="card" :class="flippedStyles" @click="selectCard">
   <div  class="card-face is-front">
      <img :src="`/images/${value}.png`" :alt="value">
      <img v-if="matched" src="/images/checkmark.svg" class="checkmark">
   </div>
   <div class="card-face is-back"></div>
  </div>
</template>
 
<script>
import { computed } from 'vue'
export default {
  name: "Card",
  props:{
    matched:{
      type: Boolean,
      default: false
    },
    value:{
      type: String,
      required: true
    },
    visible:{
      type: Boolean,
      default: false
    },
    position:{
      type: Number,
      required: true
    }
  },
  setup(props,context){
    const flippedStyles = computed(() => {
      if(props.visible){
        return 'is_flipped'
      }
      return ''
    })
    
    const selectCard = () =>{
      context.emit('select-card',{
        position: props.position,
        faceValue: props.value
      })
    }


    return{
      selectCard,
      flippedStyles
    }
  }
}
</script>
