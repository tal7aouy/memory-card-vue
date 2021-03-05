<template>
  <div class="card" @click="selectCard">
   <div v-if="visible" class="card-face is-front">
      {{ value }} - {{ position }}
   </div>
   <div v-else class="card-face is-back">
     back
   </div>
  </div>
</template>

<script>
export default {
  name: "Card",
  props:{
    matched:{
      type: Boolean,
      default: false
    },
    value:{
      type: Number,
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
    const selectCard = () =>{
      context.emit('select-card',{
        position: props.position,
        faceValue: props.value
      })
    }


    return{
      selectCard
    }
  }
}
</script>

<style>
.card{
  border: 5px solid #ccc;
  position: relative;
}
.card-face{
  width: 100%;
  height: 100%;
  position: absolute;
}
.card-face.is-front{
  background-color: red;
  color: #fff;
}
.card-face.is-back{
  background-color: blue;
  color: #fff;
}
</style>