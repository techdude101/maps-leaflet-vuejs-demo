<template>
  <div style="max-width: 15ch">
    <b-container>
  <b-row>
    <b-col></b-col>
    <b-col><b-icon-arrow-up v-b-tooltip.hover title="Move map overlay up" @click="updatePosition('up')"></b-icon-arrow-up></b-col>
    <b-col></b-col>
  </b-row> 
   <b-row>
    <b-col><b-icon-arrow-up v-b-tooltip.hover title="Move map overlay left" rotate=-90 @click="updatePosition('left')"></b-icon-arrow-up></b-col>
    <b-col></b-col>
    <b-col><b-icon-arrow-up v-b-tooltip.hover title="Move map overlay right" rotate=90 @click="updatePosition('right')"></b-icon-arrow-up></b-col>
  </b-row>
  <b-row>
    <b-col></b-col>
    <b-col><b-icon-arrow-down v-b-tooltip.hover title="Move map overlay down" @click="updatePosition('down')"></b-icon-arrow-down></b-col>
    <b-col></b-col>
  </b-row> 
</b-container>
    <!-- <b-icon-arrow-up @click="updatePosition('up')"></b-icon-arrow-up>
    <b-icon-arrow-up rotate=-90 @click="updatePosition('left')"></b-icon-arrow-up>
    <b-icon-arrow-down @click="updatePosition('down')"></b-icon-arrow-down>
    <b-icon-arrow-up rotate=90 @click="updatePosition('right')"></b-icon-arrow-up> -->
  </div>
</template>

<script>
import { BIconArrowUp, BIconArrowDown } from 'bootstrap-vue'
  export default {  
    components: {
    BIconArrowUp,
    BIconArrowDown
  },
    data() {
      return {
        up: null,
      }
    },
    methods: {
      updatePosition(direction = "left") {
        this.$emit('updatePosition', direction);
      }
    }
  }
</script>