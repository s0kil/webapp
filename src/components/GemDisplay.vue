<template>
  <div
    id="gem-display"
    :style="{flexDirection: vertical ? 'column' : 'row'}"
  >
    <img
      :width="imgWidth"
      src="../img/gem-256.png"
      :style="{'margin': `${margin}px`}"
    >
    <span
      v-if="cost"
      :style="{'font-size': `${fontSize}px`}"
    >
      {{ cost }}
    </span>
  </div>
</template>

<script>
export default {
  props: { 
    cost: {
      type: Number,
      required: false,
      default: null
    },
    size: {
      type: Number,
      required: false,
      default: 1
    },
    vertical: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  computed:{
    fontSize(){
      if (this.size === 1){
        return 24;
      }
      if (this.size === 2){
        return 32;
      }
      return 24;
    },
    margin(){
      if (this.size === 1){
        return -8;
      }
      if (this.size === 2){
        return -16;
      }
      return -8;
    },
    imgWidth(){
      if (this.size === 1){
        return 50;
      }
      if (this.size === 2){
        return 75;
      }
      return 50;
    }
  }
};
</script>

<style scoped lang="scss">
@import '@/styles/colors.scss';

#gem-display {
  color: $purple-lighter;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
</style>
