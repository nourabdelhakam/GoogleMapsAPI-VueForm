<template>
  <v-app>
    <v-content>
      <v-container class="my-5 ">
        <v-card shaped elevation="8">
        <v-layout row wrap justify-space-around>
          <v-flex xs12 md5>
            <v-container class="my-5">
              <v-card-title class="px-5 py-5">Complete the form</v-card-title>
              <v-form v-model="valid" ref="form" >
               <v-text-field  label="Name" v-model="name" :rules="namelRules" required prepend-icon="person" class="px-3 my-3"></v-text-field>
               <v-text-field label="E-mail" v-model="email" :rules="emailRules" required prepend-icon="email" class="px-3 my-5"></v-text-field>
               <v-text-field label="Mobile" type="tel" v-model="mobile" :rules="mobileRules" required prepend-icon="phone" class="px-3 my-5"></v-text-field>
               <div class="v-input">
                  <v-icon class="px-3"> my_location</v-icon>
                    <gmap-autocomplete @place_changed="setPlace" style="width:70%;  height: 35px;"></gmap-autocomplete>
                    <!-- <v-btn @click="addMarker" class=" ml-7"><v-icon>room</v-icon></v-btn> -->
                </div>
              <v-card-actions>
               <v-btn class="mt-5" @click="submit">Submit</v-btn>
               </v-card-actions>
               </v-form>
            </v-container>
          </v-flex>

          <v-flex xs12 md5>
            <v-container class="my-5">
              <gmap-map :center="center" :zoom="12" style="width:100%;  height: 500px;">
                <gmap-marker :key="index" v-for="(m, index) in markers" :position="m.position" @click="center=m.position"></gmap-marker>
              </gmap-map>
            </v-container>
          </v-flex>
        </v-layout>
        </v-card>
      </v-container>
    </v-content>
  </v-app>
</template>


<script>

export default {
   name: "App",
   data() {
        return {
            valid: false,
            name: '',
            namelRules: [
                v => !!v || 'Name is required',
            ],
            email: '',
            emailRules: [
                v => !!v || 'E-mail is required',
                v => /.+@.+/.test(v) || 'E-mail must be valid',
            ],
            mobile: '',
            mobileRules: [
                v => !!v || 'Mobile number is required',
                v => v.length == 11 || 'Mobile number must be 11 digits.',
            ],
            address: '',

            center: { lat: 45.508, lng: -73.587 },
            markers: [],
            places: [],
            currentPlace: null
        }
    },
    mounted() {
    this.geolocate();
    },
    methods: {
        submit() {
            if(this.$refs.form.validate()) {
                console.table(this.name, this.email, this.mobile, this.address);
            } 
        },

      
    setPlace(place) {
      this.currentPlace = place;
      this.addMarker()
    },
    addMarker() {
      if (this.currentPlace) {
        const marker = {
          lat: this.currentPlace.geometry.location.lat(),
          lng: this.currentPlace.geometry.location.lng()
        };
        this.markers.push({ position: marker });
        this.places.push(this.currentPlace);
        this.address = {
          address:this.currentPlace.formatted_address,
          lat:this.currentPlace.geometry.location.lat(),
          lng:this.currentPlace.geometry.location.lng()
        }
        this.center = marker;
        this.currentPlace = null;
      }
    },
    geolocate: function() {
      navigator.geolocation.getCurrentPosition(position => {
        this.center = {
          lat: position.coords.latitude,
          lng: position.coords.longitude
        };
      });
    }
  }
}
  


</script>

<style>
#app {
  font-family: Arial, Helvetica, sans-serif;
  text-align: center;
  color: #2c3e50;
  background-color: #f3f7fa
}
</style> 
