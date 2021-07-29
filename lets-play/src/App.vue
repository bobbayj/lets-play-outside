<template>
  <div id="app">
    <!--Navbar Here -->
    <div>
      <nav>
        <div class="header">
          <h3>Let's Play</h3>
          <button id='find-me'
            type="button"
            :disabled="loading"
            :class="{ disabled: loading}"
            class='location-btn'
            @click="findme">Get my location</button>
        </div>
      </nav>
    </div>
    <!--Index Page Here -->
    <index :user_pos="location"/>
  </div>
</template>
<script>
import index from "./components/Index.vue";
export default {
  name: "App",
  components: {
    index,
  },
  data() {
    return {
      location: {lat:-33.868, lng:151.21},
      loading: false,
      errorStr: null,
    };
  },
  methods: {
    findme() {
      // do we support gelocation
      if (!navigator.geolocation) {
        alert('Geolocation is not supported by your browser');
        return;
      }

      this.loading = true
      // get position
      
      navigator.geolocation.getCurrentPosition( pos => {
        this.location = {lng: pos.coords.longitude, lat: pos.coords.latitude};
        console.log("Location found", this.location)
        this.loading = false
      }, err => {
        this.errorStr = err.message;
        console.log(this.errorStr)
      })
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
html,
body {
  background: #fafcfd;
  margin: 0;
}
nav {
  background: #ffffff;
  box-shadow: 0px 2px 5px rgba(0, 58, 78, 0.15);
}
.header {
  display: flex;
  flex-direction: row;
  align-items: center;
  padding: 0px 50px;
}
.header h3 {
  color: #1f2a53;
  font-weight: 600;
}

.location-btn {
  margin-left: auto;
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
.location-btn:focus {
  outline: none;
}

.disabled {
  background: #db7990;
  cursor: not-allowed;
}
</style>