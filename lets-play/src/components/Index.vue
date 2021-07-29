<template>
  <div class="main">
    <div class="flex">
      <!-- Map Display here -->
      <div class="map-holder">
        <div id="map"></div>
        <div id='info' class='info'></div>
      </div>

      <!-- Coordinates Display here -->
      <div class="dislpay-arena">
        <div class="coordinates-header">
          <h3>Current Coordinates</h3>
          <p>Latitude: {{ map_center.lat }}</p>
          <p>Longitude: {{ map_center.lng }}</p>
          <p>Location tags: {{ category }} </p>
          <p>Your location: {{ this.user_pos }} </p>
        </div>

        <div class="coordinates-header">
          <h3>Current Location</h3>

          <div class="form-group">
            <input
              type="text"
              class="location-control"
             :value="location"
              readonly
            />
            <button type="button" class="copy-btn" @click="copyLocation">
              Copy
            </button>
          </div>

          <button
            type="button"
            :disabled="loading"
            :class="{ disabled: loading}"
            class="action-btn"
            @click="getLocation"
          >
            Get Location (to remove)
          </button><br>
          <button
            type="button"
            :disabled="loading"
            :class="{ disabled: loading}"
            class="action-btn"
            @click="removeMarkers"
          >
            Remove Markers
          </button><br>
          <button
            type="button"
            :disabled="loading"
            :class="{ disabled: loading}"
            class="action-btn"
            @click="searchAroundUser"
          >
            Search Places
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import mapboxgl from "mapbox-gl";
import axios from "axios";
import MapboxGeocoder from "@mapbox/mapbox-gl-geocoder";
import "@mapbox/mapbox-gl-geocoder/dist/mapbox-gl-geocoder.css";
export default {
  props: ['user_pos'],
  data() {
    return {
      loading: false,
      location: "",
      category: "",
      access_token: process.env.VUE_APP_MAP_ACCESS_TOKEN,
      map_center: this.user_pos,
      map: {},
      currentMarkers: [],
      userMarker: null,
      nearby_results: [],
    };
  },
  mounted() {
    this.createMap();
  },
  watch: {
    // whenever the user position changes, reset centre and drop a market
    user_pos: {
      handler() {
        console.log('Flying...')
        this.flyToUser();
      },
      deep: true
    }
  },
  methods: {
    async createMap() {
      try {
        mapboxgl.accessToken = this.access_token;
        this.map = new mapboxgl.Map({
          container: "map",
          style: "mapbox://styles/mapbox/streets-v11",
          center: this.map_center,
          zoom: 11,
          attributionControl: false,
        })
          .addControl(new mapboxgl.NavigationControl(), 'top-left')
          .addControl(new mapboxgl.AttributionControl({
            compact: true
          })
          );
        
        let geocoder =  new MapboxGeocoder({
            accessToken: this.access_token,
            mapboxgl: mapboxgl,
            marker: false,
          }); 
        this.map.addControl(geocoder);
        
        geocoder.on("result", (e) => {
          const marker = new mapboxgl.Marker({
            draggable: true,
            color: "#D80739",
          })
            .setLngLat(e.result.center)
            .addTo(this.map);
            this.currentMarkers.push(marker)
          
          this.map_center = e.result.center;

          marker.on("dragend", (e) => {
            this.map_center = Object.values(e.target.getLngLat());
          });
        });
      } catch (err) {
        console.log("map error", err);
      }
    },
    async getLocation() {
      try {
        this.loading = true;
        const response = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/`
          + `${this.map_center.lng},${this.map_center.lat}.json?`
          + `access_token=${this.access_token}`
          // + `&types=poi`
          // + `&proximity=Lon,Lat`
          // + `&bbox=minLon,minLat,maxLon,maxLat`
        );
        this.loading = false;
        this.location = response.data.features[0].place_name;
        this.category = response.data.features[0].properties.category
      } catch (err) {
        this.loading = false;
        console.log(err);
      }
    },
    async removeMarkers() {
      if (this.currentMarkers!==null) {
        for (var i = this.currentMarkers.length - 1; i >= 0; i--) {
        this.currentMarkers[i].remove();
        }
      }
    },
    copyLocation() {
      if (this.location) {
        navigator.clipboard.writeText(this.location);
        alert("Location Copied")
      }
      return;
    },
    async flyToUser() {
      if (!this.userMarker) {
        const userMarker = new mapboxgl.Marker({
          draggable: true,
          color: "#3A9CFF",
        })
          .setLngLat(this.user_pos)
          .addTo(this.map);
        this.userMarker = userMarker
      } else {
        this.userMarker.setLngLat(this.user_pos)
      }

      this.map.flyTo({
        center: this.user_pos,
        zoom: 14,
        essential: true // this animation is considered essential with respect to prefers-reduced-motion
      });
    },
    async searchAroundUser() {
      try {
        this.loading = true;
        const response = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/`
          + `${this.user_pos.lng},${this.user_pos.lat}.json?`
          + `access_token=${this.access_token}`
          + `&types=poi`
          + `&limit=10`
          + `&proximity=${this.user_pos.lng},${this.user_pos.lat}`
          // + `&bbox=${this.user_pos.lng - 0.1},${this.user_pos.lat - 0.1},${this.user_pos.lng + 0.1},${this.user_pos.lat + 0.1}`
        );
        this.loading = false;
        this.nearby_results.push(response.data)

        //  Add a marker at each result's coordinates
        for (const result of this.nearby_results) {

          this.map.addSource('search results', {
            type: 'geojson',
            data: {
              type: 'FeatureCollection',
              features: [],
            },
          })
          this.map.addLayer({
            id: 'point',
            source: 'search results',
            type: 'circle',
            paint: {
              'circle-radius': 10,
              'circle-color': '#448ee4'
            },
          });
          this.map.getSource('search results').setData(result)
        }
      } catch (err) {
        this.loading = false;
        console.log(err);
      }
    },
  },
};
</script>

<style scoped>
.main {
  padding: 45px 50px;
}
.flex {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.map-holder {
  width: 65%;
}
#map {
  height: 70vh;
}
.dislpay-arena {
  background: #ffffff;
  box-shadow: 0px -3px 10px rgba(0, 58, 78, 0.1);
  border-radius: 5px;
  padding: 20px 30px;
  width: 25%;
}
.coordinates-header {
  margin-bottom: 50px;
}
.coordinates-header h3 {
  color: #1f2a53;
  font-weight: 600;
}
.coordinates-header p {
  color: rgba(13, 16, 27, 0.75);
  font-weight: 600;
  font-size: 0.875rem;
}
.form-group {
  position: relative;
}
.location-control {
  height: 30px;
  background: #ffffff;
  border: 1px solid rgba(31, 42, 83, 0.25);
  box-shadow: 0px 0px 10px rgba(73, 165, 198, 0.1);
  border-radius: 4px;
  padding: 0px 10px;
  width: 90%;
}
.location-control:focus {
  outline: none;
}
.action-btn {
  margin-top: 15px;
  padding: 10px 15px;
  background: #d80739;
  box-shadow: 0px 4px 10px rgba(73, 165, 198, 0.1);
  border-radius: 5px;
  border: 0;
  cursor: pointer;
  color: #ffffff;
  font-size: 0.875rem;
  font-weight: 600;
}
.action-btn:focus {
  outline: none;
}
.disabled {
  background: #db7990;
  cursor: not-allowed;
}
.copy-btn {
  background: #f4f6f8 0% 0% no-repeat padding-box;
  border: 1px solid #f4f6f8;
  border-radius: 0px 3px 3px 0px;
  position: absolute;
  color: #5171ef;
  font-size: 0.875rem;
  font-weight: 500;
  height: 30px;
  padding: 0px 10px;
  cursor: pointer;
  right: 3.5%;
  top: 5%;
}
.copy-btn:focus {
  outline: none;
}
.info {
  position:relative;
  bottom:10px;
  right:10px;
  }
  .info div {
    background:#fff;
    padding:10px;
    border-radius:3px;
    }
</style>
