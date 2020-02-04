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

      <template
        v-for="({ type, sourceProps, visible, layerProps, opacity },
        index) in config"
      >
        <template v-if="visible">
          <vl-layer-tile v-if="type === 'wms'" :key="index" v-bind="layerProps">
            <vl-source-wms
              v-bind="sourceProps"
              :transition="parseInt(opacity)"
            ></vl-source-wms>
          </vl-layer-tile>

          <vl-layer-vector v-if="type === 'wfs'" :key="index">
            <vl-source-vector v-bind="sourceProps"></vl-source-vector>
          </vl-layer-vector>
        </template>
      </template>
    </vl-map>

    <div>
      Слои <br />
      <template v-for="(layer, index) in config">
        <input type="checkbox" :key="index" v-model="layer.visible" />
        {{ layer.name }}, opacity:
        <input
          type="number"
          min="0"
          max="1"
          step="0.1"
          :key="'n' + index"
          v-model="layer.layerProps.opacity"
        />
        <button v-if="index > 0" :key="'up' + index" @click="up(layer, index)">
          ↑
        </button>
        <button
          v-if="index < config.length - 1"
          :key="'down' + index"
          @click="down(layer, index)"
        >
          ↓
        </button>
        <br :key="'br' + index" />
      </template>
    </div>
    <br />
    <div>
      Zoom: {{ zoom }}<br />
      Center: {{ center }}<br />
      Rotation: {{ rotation }}<br />
      My geolocation: {{ geolocPosition }}
    </div>
  </div>
</template>

<script>
function arrayMove(arr, old_index, new_index) {
  if (new_index >= arr.length) {
    var k = new_index - arr.length + 1;
    while (k--) {
      arr.push(undefined);
    }
  }
  arr.splice(new_index, 0, arr.splice(old_index, 1)[0]);
  return arr;
}

export default {
  name: "Map",
  props: ["initial-config"],
  data() {
    return {
      zoom: 2,
      center: [0, 0],
      rotation: 0,
      geolocPosition: undefined,
      config: this.initialConfig.map(l => ({ ...l, visible: true }))
    };
  },
  watch: {
    geolocPosition: function(val) {
      this.center = val;
    }
  },
  methods: {
    up: function(el, idx) {
      arrayMove(this.config, idx, idx - 1);
    },
    down: function(el, idx) {
      arrayMove(this.config, idx, idx + 1);
    }
  }
};
</script>
