<template>
  <div id="map"></div>
</template>

<script>
import "leaflet/dist/leaflet.css";
import L from "leaflet";
require("leaflet.markercluster");
import "leaflet.markercluster/dist/MarkerCluster.css";
import "leaflet.markercluster/dist/MarkerCluster.Default.css";
import axios from "axios";

export default {
  data: function () {
    return {
      map_link: "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
      mapOptions: {
        center: [35.6825, 139.752778],
        zoom: 12,
      },
      stationList: [],
      selectedSample: [],
    };
  },
  created: function () {
    this.getStationList();
  },
  mounted: function () {
    this.map = new L.map("map", this.mapOptions);
    this.map.addLayer(new L.TileLayer(this.map_link));
  },
  watch: {
    // watch:監視プロパティ
    stationList: function () {
      this.renderMarker();
    },
  },
  methods: {
    getStationList: function () {
      axios
        .get("http://localhost:8080/group/station")
        .then((response) => {
          this.stationList = response.data;
          console.log(this.stationList);
        })
        .catch((error) => console.log(error));
      console.log(this.stationList);
    },
    renderMarker: function () {
      const markerOptions = {
        icon: L.divIcon({ className: "red marker", iconSize: [16, 16] }),
      };

      var markerGroup = L.markerClusterGroup();

      for (var i = 0; i < this.stationList.length; i++) {

        for (var j = 0; j < this.stationList[i].Count; j++) {
          var marker = new L.Marker(
            [this.stationList[i].Ido, this.stationList[i].Keido],
            markerOptions
          );
          marker.addTo(markerGroup);
          marker.bindPopup(`
            <div class="">駅名：${this.stationList[i].Station}</div>
            <div class="">案件数：${this.stationList[i].Count}</div>
            <div class="">平均単価：${this.stationList[i].Avg}円</div>
            <div class="">最大単価：${this.stationList[i].Max}円</div>
            <div class="">最小単価：${this.stationList[i].Min}円</div>
        `);
        }
      }
      markerGroup.addTo(this.map);
    },
    markerClicked: function (mrkr_data) {
      this.selectedSample = mrkr_data.derivation_code;
      console.log(this.selectedSample);
    },
  },

  // mounted() {
  //     var resJson
  //     axios.get('http://localhost:8080/group/station').then(
  //         response => resJson = response.data)

  //     const w = { icon: L.divIcon( { className: 'red marker', iconSize: [ 16, 16 ] } ) }
  //     L.map( 'app', { center: L.latLng( 35.6825, 139.752778 ), zoom: 12 } ).addLayer(
  //         L.tileLayer( 'http://{s}.tile.osm.org/{z}/{x}/{y}.png' )
  //     ).addLayer(
  //         L.markerClusterGroup().addLayer(
  //             L.marker( [ 35.691, 139.752 ], w )
  //         ).addLayer(
  //             L.marker( [ 35.691, 139.752 ], w )
  //         ).addLayer(
  //             L.marker( [ 35.691, 139.752 ], w )
  //         ).addLayer(
  //             L.marker( [ 35.691, 139.752 ], w )
  //         ).addLayer(
  //             L.marker( [ 35.691, 139.752 ], w )
  //         ).addLayer(
  //             L.marker( [ 35.691, 139.752 ], w )
  //         )
  //     )
  // }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#map {
  height: 100%;
}
body {
  margin: 0;
}

.marker {
  text-align: center;
  color: white;
  font-size: 16;
  border-radius: 8px;
  box-shadow: 8px 8px 8px rgba(0, 0, 0, 0.4);
}
.red {
  background: red;
}
</style>
