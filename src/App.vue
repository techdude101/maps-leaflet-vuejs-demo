<template>

<div style="height: 60vh; width: 80%; margin: 0 auto">
    <div>
      <input type="text" placeholder="latitude, longitude" v-model="overlayNorthEastBounds">
      <input type="text" placeholder="latitude, longitude" value="overlaySouthWestBounds" v-model="overlaySouthWestBounds">
      <button @click="updateOverlayBounds">Update</button>
    </div>
    <br />
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
    </l-map>
    <h2 style="margin: 0 auto; text-align: center">Centre is at {{ currentCenter }} and the zoom is: {{ currentZoom }}</h2>
    <br />
    <h2 style="margin: 0 auto; text-align: center">Bounds: {{ `${currentBounds._southWest}, ${currentBounds._northEast}` }}</h2>
    <br />
    <h2 style="margin: 0 auto; text-align: center">Overlay bounds: {{ overlayBounds }}</h2>
    <br />
    <center>
      <button @click="handleClick">Show / Hide Overlay</button>
      <br />
      <button @click="moveLeft">Left</button>
      <button @click="moveRight">Right</button>
      <br />
      <input type="checkbox" v-model="fineAdjustment" name="fine-adjustment" />
      <label for="fine-adjustment"> Enable Fine Adjustment</label>
    </center>
  </div>

</template>

<script>
import { latLng } from "leaflet";
import logo from "./assets/logo.png";
import overlayMap from "./assets/Cycle_Route_Map_Belfast_City.png";
import { LMap, LTileLayer, LImageOverlay } from "vue2-leaflet";

export default {
  name: "Example",
  components: {
    LMap,
    LTileLayer,
    LImageOverlay,
    // LMarker,
    // LPopup,
    // LTooltip
  },
  data() {
    return {
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
      // let north = this.overlayBounds[0][0];
      // let east = this.overlayBounds[0][1];
      // let south = this.overlayBounds[1][0];
      // let west = this.overlayBounds[1][1];
      // east -= 1 / (this.currentZoom * 100);
      // west -= 1 / (this.currentZoom * 100);
      // this.overlayBounds = [[north, east], [south, west]];
    },
    moveRight() {
      if (this.fineAdjustment) {
        this.overlayBounds = this.moveOverlay("right", 9999);  
      } else {
        this.overlayBounds = this.moveOverlay("right", 500);
      }
      // let north = this.overlayBounds[0][0];
      // let east = this.overlayBounds[0][1];
      // let south = this.overlayBounds[1][0];
      // let west = this.overlayBounds[1][1];
      // east += 1 / (this.currentZoom * 20);
      // west += 1 / (this.currentZoom * 20);
      // this.overlayBounds = [[north, east], [south, west]];
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
    showLongText() {
      this.showParagraph = !this.showParagraph;
    },
    innerClick() {
      alert("Click!");
    }
  }
};
</script>

<style>

</style>