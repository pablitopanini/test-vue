<template>
  <div>
    <vl-map
      :load-tiles-while-animating="true"
      :load-tiles-while-interacting="true"
      data-projection="EPSG:4326"
      style="height: 600px"
    >
      <vl-view
        :zoom.sync="zoom"
        :center.sync="center"
        :rotation.sync="rotation"
      ></vl-view>

      <vl-geoloc @update:position="geolocPosition = $event">
        <template slot-scope="geoloc">
          <vl-feature v-if="geoloc.position" id="position-feature">
            <vl-geom-point :coordinates="geoloc.position"></vl-geom-point>
            <vl-style-box>
              <vl-style-icon
                :src="require('../assets/logo.png')"
                :scale="0.1"
                :anchor="[0.5, 1]"
              ></vl-style-icon>
            </vl-style-box>
          </vl-feature>
        </template>
      </vl-geoloc>

      <vl-layer-tile id="osm">
        <vl-source-osm></vl-source-osm>
      </vl-layer-tile>

      <vl-layer-tile id="wms" :extent="[-13884991, 2870341, -7455066, 6338219]">
        <vl-source-wms
          :url="'https://ahocevar.com/geoserver/wms'"
          :cross-origin="'anonymous'"
          :server-type="'geoserver'"
          :transition="0"
          :layers="'topp:states'"
          :tiled="true"
        ></vl-source-wms>
      </vl-layer-tile>

      <vl-layer-vector>
        <vl-source-vector
          :url="
            'https://openlayers.org/en/latest/examples/data/geojson/countries.geojson'
          "
        ></vl-source-vector>
      </vl-layer-vector>
    </vl-map>
    <!-- <div style="padding: 20px">
      Zoom: {{ zoom }}<br />
      Center: {{ center }}<br />
      Rotation: {{ rotation }}<br />
      My geolocation: {{ geolocPosition }}
    </div> -->
  </div>
</template>

<script>
export default {
  name: "Map",
  data() {
    return {
      zoom: 2,
      center: [0, 0],
      rotation: 0,
      geolocPosition: undefined
    };
  },
  watch: {
    geolocPosition: function(val) {
      this.center = val;
      //   this.zoom = 14;
    }
  }
};
</script>
