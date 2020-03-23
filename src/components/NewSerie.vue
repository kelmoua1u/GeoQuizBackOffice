<template>
  <div>
    <div>
      <h2>Veuillez saisir la Ville</h2>
      <label>
        <gmap-autocomplete
          @place_changed="setPlace">
        </gmap-autocomplete>
        <button @click="loadMap">Chercher</button>
      </label>
      <br/>

    </div>
    <br>
    <div>
    <gmap-map 
    id="map"
      :center="center"
      :zoom="13"
      :options="mapOptions"
      @click="getResult"
      style="width:40%;  height: 400px; margin-left:100px; float:left; "
      
    >
      <gmap-marker
        :key="index"
        v-for="(m, index) in markers"
        :position="m.position"
        @click="center=m.position"
      ></gmap-marker>
    </gmap-map>

    <img src="https://media-cdn.tripadvisor.com/media/photo-s/14/4f/19/94/bab-sidi-abdelwahab.jpg" style="width:500px; height:350px; margin-left:120px;"></div>
    <br> <br><div> <h1><p>La distance en metre est :{{distance}}  </p></h1></div>
    <div> <h1><p>Votre score est :{{scoreAct}}  </p></h1></div>
  </div>
</template>

<script>
export default {
  name: "GoogleMap",
  data() {
    return {
      // default to Montreal to keep it simple
      // change this to whatever makes sense
      center: { lat: 45.508, lng: -73.587 },
      markers: [],
      places: [],
      currentPlace: null,
      mapOptions: {
      controlSize: 20,
      clickableIcons: false,
      draggable: true,
      zoomControl: true,
      mapTypeControl: true,
      scaleControl: false,
      streetViewControl: false,
      rotateControl: false,
      fullscreenControl: false,
      styles: [
        {
          featureType: "poi",
          stylers: [{ visibility: "off" }]
        }
      ]
    },
    ltPicture:'34.67998257948112 ',
    lgPicture:'-1.9105777372448673',
    distance:0,
    isPaused: false,
    isSaving: false,
    showPause: true,
    showNext: false,
    startTime: Date.now(),
    seconds: 0,
    scoreAct: 0,
    pauseTime: null,
    timePaused: 0
    };
  },

  mounted() {
    this.geolocate();
    
   
  },

  methods: {
    // receives a place object via the autocomplete component
    setPlace(place) {
      this.currentPlace = place;
    },
    addMarker() {
      if (this.currentPlace) {
        const marker = {
          lat: this.currentPlace.geometry.location.lat(),
          lng: this.currentPlace.geometry.location.lng()
        };
        this.markers.push({ position: marker });
        this.places.push(this.currentPlace);
        this.center = marker;
        this.currentPlace = null;
      }
    },
    loadMap() {
       
        this.center = {
          lat: this.currentPlace.geometry.location.lat(),
          lng: this.currentPlace.geometry.location.lng()
          
        };
      
    },
    geolocate: function() {
      navigator.geolocation.getCurrentPosition(position => {
        this.center = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        };
      });
    },
    
    async getResult(cursorPos) {
        const marker={
            lat: cursorPos.latLng.lat(),
          lng:cursorPos.latLng.lng()
        };
        
        this.markers.push({ position: marker });
        this.places.push(this.currentPlace);
        this.center = marker;
        this.currentPlace = null;
        this.getDistance(marker);
        this.calculScore();
        
     /* alert(cursorPos.latLng.lat()+" et"+cursorPos.latLng.lng());*/
    },
    getDistance(p1) {
      const rad = function(x) {
        return (x * Math.PI) / 180;
      };
      /*alert(this.lgPicture);*/
      const R = 6378137; // Earth’s mean radius in meter
      const dLat = rad(this.ltPicture - p1.lat);
      const dLong = rad(this.lgPicture - p1.lng);
      
      const a =
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos(rad(p1.lat)) *
          Math.cos(rad(this.ltPicture)) *
          Math.sin(dLong / 2) *
          Math.sin(dLong / 2);
      const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    this.distance= Math.round(R * c); // returns the distance in meter
    const marker={
            lat: this.ltPicture,
          lng:this.lgPicture
        };
        this.markers.push({ position: marker });
        this.places.push(this.currentPlace);
        this.center = marker;
        this.currentPlace = null;
    
    },
    calculScore() {
      

      if (this.distance < 200) {
        this.scoreAct += 5;
      } else if (this.distance < 2 * 200) {
        this.scoreAct += 3;
      } else if (this.distance < 3 * 200) {
        this.scoreAct += 1;
      }

      const timePlayed = this.seconds - this.timePaused;

      if (timePlayed < 15) {
        this.scoreAct *= 4;
      } else if (timePlayed >= 15 && timePlayed < 30) {
        this.scoreAct *= 2;
      } else if (timePlayed >= 30 && timePlayed < 60) {
        this.scoreAct *= 1;
      } else if (timePlayed > 60) {
        this.scoreAct = 0;
      }
    
     
    }

  }
};
</script>