<!-- ol-marker.vue -->
<template>
  <div></div>
</template>
<style>
  /* TODO vale expor a complexidade das 'features' do openlayer aqui? */
</style>
<!-- <script src="./js/OpenLayers.js"></script> -->
<!-- <script type="text/javascript" src="http://dev.openlayers.org/OpenLayers.js"></script> -->
<script>

</script>
<script>

  const ol = require('openlayers')
  module.exports = {
    name: 'OLMarker',
    props: {
      coords: {
        type: Array,
        default: () => [-87.758888, 41.973387]
      },
      markerData: Object,
      iconImageUrl: String
    },

    data () {
      return {
        feature: null,
        style: null,
        vectorSource: null,
        vectorLayer: null,
        newcoords: null
      }
    },
    watch: {
      coords (e) {
        this.setpos(e)
      }
    },
    mounted () {
      // http://openlayers.org/en/latest/examples/icon-color.html?q=feature
      console.log(this.coords)
      this.feature = new ol.Feature({
        geometry: new ol.geom.Point(ol.proj.fromLonLat(this.coords))
      })
      this.feature.vueComponent = this
      if (this.iconImageUrl) {
        this.style = new ol.style.Style({
          image: new ol.style.Icon({
            src: this.iconImageUrl,
            anchor: [0.5, 1]
          })
        })
      } else {
        this.style = new ol.style.Style({
          image: new ol.style.Circle({
            radius: 10,
            snapToPixel: true,
            fill: new ol.style.Fill({color: 'green'}),
            stroke: new ol.style.Stroke({
              color: 'white', width: 3
            })
          })
        })
      }
      this.feature.setStyle(this.style)
      this.vectorSource = new ol.source.Vector({
        features: [this.feature]
      })
      this.vectorLayer = new ol.layer.Vector({
        source: this.vectorSource
      })
      this.$parent.addMarker(this.vectorLayer)// .olmap.addLayer(vectorLayer)
    },

    methods: {
      tocentermap (e) {
        const center = e.map.getView().getCenter()
        const lonlat = ol.proj.toLonLat(center)
        this.setpos(lonlat)
      },
      setpos (lonlat) {
        this.newcoords = lonlat
        this.feature.setGeometry(new ol.geom.Point(ol.proj.fromLonLat(lonlat)))
        this.$emit('newcoords', this.newcoords)
      }
    }
  }
</script>
