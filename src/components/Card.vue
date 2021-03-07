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

<style>
.card{
  position: relative;
  transition: .5s transform ease-in;
  transform-style: preserve-3d;
}
.card.is_flipped{
 transform: rotateX(180deg);

}
.card-face{
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: .5s ease-in-out;
  backface-visibility: hidden;
}
.card-face:hover{
  cursor: pointer;
   transform: scale(1.2);
}
.card-face.is-front{
  background-image: url('/images/card-front.png');
  color: #fff;
  transform: rotateX(180deg);
}
.card-face.is-back{
  background-image: url('/images/card-back.png');
  color: #fff;
}
.checkmark{
  position: absolute;
  left: 5px;
  top: 5px;
  width: 20px;
  height: 20px;
}
</style>