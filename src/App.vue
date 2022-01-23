<template>
<div class="text-center" style="height: 60vh; width: 80%; margin: 1rem auto; padding: 1rem 0rem;">
  <h1 class="text-center" style="padding: 1rem;">Leaflet Map Overlay Demo</h1>
  <b-form-select class="select" v-model="selected" :options="options" @change="handleOverlayChange"></b-form-select>
    <l-map
      v-if="showMap"
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      style="height: 100%"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
      @update:bounds="boundsUpdate"
    >
      <l-tile-layer
        :url="url"
        :attribution="attribution"
      />
      <l-image-overlay :url="overlayURL" :bounds="overlayBounds" :visible="showOverlay"></l-image-overlay>
      <l-rectangle :bounds="overlayBounds" :l-style="rectangle.style"></l-rectangle>
      <!-- <l-image-overlay :url="overlayURL" :bounds="overlayBounds" :visible="false"></l-image-overlay> -->
    </l-map>
    <!-- TODO: Upload image & restrict file types (disable show "All files") -->
      <!-- <image-upload></image-upload> -->
      <div style="display: flex; justify-content: center; align-items: center; flex-direction: column;">
        <h1>Position</h1>
      <joystick @updatePosition="handleUpdatePosition"></joystick>
      </div>
      <b-form-checkbox
        v-b-tooltip.hover.bottom title="Enable or disable fine adjustment of map overlay"
        id="fine-adjustment"
        v-model="fineAdjustment"
      >
      &nbsp;Fine adjustment
      </b-form-checkbox>
  </div>

</template>

<script>
import { latLng } from "leaflet";
import logo from "./assets/logo.png";
import overlayMap from "./assets/Cycle_Route_Map_Belfast_City.png";
import overlayMapNoBackground from "./assets/Cycle_Route_Map_Belfast_City_nobg.png";
import { LMap, LTileLayer, LImageOverlay, LRectangle } from "vue2-leaflet";
// import ImageUpload from './ImageUpload.vue';
import Joystick from './Joystick.vue';

export default {
  name: "LeafletDemo",
  components: {
    LMap,
    LTileLayer,
    LImageOverlay,
    LRectangle,
    // ImageUpload,
    Joystick
  },
  data() {
    return {
      rectangle: {
        bounds: [[54.60222908456487, -5.927228491440711], [54.5969999250192, -5.908268890668636]],
        style: { color: 'red', weight: 1, fillOpacity: 0.1 }
      },
      selected: overlayMap,
      options: [
        { value: overlayMap, text: 'Cycle route' },
        { value: overlayMapNoBackground, text: 'Cycle route no background' },
      ],
      fineAdjustment: false,
      logo: logo,
      zoom: 13,
      center: latLng(54.599843, -5.9206),
      url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      overlayURL: overlayMap,
      overlayNorthEastBounds: [0, 0],
      overlaySouthWestBounds: [0, 0],
      // Cycle route map - 54.60222908456487, -5.927228491440711 | 54.5969999250192, -5.908268890668636
      overlayBounds: [[54.60222908456487, -5.927228491440711], [54.5969999250192, -5.908268890668636]],
      showOverlay: true,
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      currentBounds: 0,
      showParagraph: false,
      mapOptions: {
        zoomSnap: 0.5
      },
      showMap: true
    };
  },
  methods: {
    handleOverlayChange() {
      this.overlayURL = this.selected;
    },
    handleUpdatePosition(direction = "left") {
      if (this.fineAdjustment) {
        this.overlayBounds = this.moveOverlay(direction, 9999);  
      } else {
        this.overlayBounds = this.moveOverlay(direction, 500);
      }
    },
    updateOverlayBounds() {
      // Convert text to number "12.34,56.78" -> 12.34,56.78
      const north = Number(this.overlayNorthEastBounds.split(',')[0]);
      const east = Number(this.overlayNorthEastBounds.split(',')[1]);

      const south = Number(this.overlaySouthWestBounds.split(',')[0]);
      const west = Number(this.overlaySouthWestBounds.split(',')[1]);

      this.overlayBounds = [[north, east], [south, west]];
    },
    handleClick() {
      this.showOverlay = !this.showOverlay;
    },
    moveOverlay(direction = "left", factor = 10) {
      let north = this.overlayBounds[0][0];
      let east = this.overlayBounds[0][1];
      let south = this.overlayBounds[1][0];
      let west = this.overlayBounds[1][1];

      switch (direction) {
        case "left":
          east -= 1 / (this.currentZoom * factor);
          west -= 1 / (this.currentZoom * factor);
          break;
        case "right":
          east += 1 / (this.currentZoom * factor);
          west += 1 / (this.currentZoom * factor);
          break;
        case "up":
          north += 1 / (this.currentZoom * factor);
          south += 1 / (this.currentZoom * factor);
          break;
        case "down":
          north -= 1 / (this.currentZoom * factor);
          south -= 1 / (this.currentZoom * factor);
          break;
      } // End switch direction
      
      return [[north, east], [south, west]];
    },
    moveLeft() {
      if (this.fineAdjustment) {
        this.overlayBounds = this.moveOverlay("left", 9999);  
      } else {
        this.overlayBounds = this.moveOverlay("left", 500);
      }
    },
    moveRight() {
      if (this.fineAdjustment) {
        this.overlayBounds = this.moveOverlay("right", 9999);  
      } else {
        this.overlayBounds = this.moveOverlay("right", 500);
      }
    },
    boundsUpdate(bounds) {
      this.currentBounds = bounds;
    },
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
      this.overlayNorthEastBounds = this.overlayBounds[0];
      this.overlaySouthWestBounds = this.overlayBounds[1];
    },
  }
};
</script>

<style>
.select {
  font-size: 20px;
  margin: 1em;
}
</style>