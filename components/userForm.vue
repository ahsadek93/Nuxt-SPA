<template>
  <v-alert outlined color="blue-grey">
    <v-col cols="12">
      <GmapMap
        v-if="showMap"
        :center="currentLocation"
        :zoom="17"
        map-type-id="terrain"
        style=" height: 300px"
      >
        <GmapMarker
          :position="currentLocation"
          :clickable="false"
          :draggable="false"
          @click="center=currentLocation"
        />
      </GmapMap>
    </v-col>

    <v-form>
      <v-container>
        <v-row class="justify-space-between">
          <v-col cols="3">
            <v-text-field label="First name"></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field label="Last name"></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field label="Email"></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field label="Phone number"></v-text-field>
          </v-col>
          <v-col cols="12">
            <v-btn @click="getLocation">Get Current Address</v-btn>
          </v-col>
          <v-col cols="3">
            <v-text-field v-model="userFormData[0].address.street" disabled label="Street Name"></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field
              v-model="userFormData[0].address.neighborhood"
              disabled
              label="Neighborhood"
            ></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field v-model="userFormData[0].address.city" disabled label="City"></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field v-model="userFormData[0].address.state" disabled label="State"></v-text-field>
          </v-col>
          <v-col cols="3">
            <v-text-field v-model="userFormData[0].address.country" disabled label="Country"></v-text-field>
          </v-col>
        </v-row>
      </v-container>
    </v-form>
  </v-alert>
</template>

<script>
import Vue from "vue";
import * as VueGoogleMaps from "vue2-google-maps";
import axios from "@nuxtjs/axios";

Vue.use(VueGoogleMaps, {
  load: {
    key: "AIzaSyAc_W5NukW2Uy-fAs6GA2YiJN9rbluyYpU",
    libraries: "places"
  }
});

export default {
  name: "userForm",
  data() {
    return {
      currentLocation: { lat: 0, lng: 0 },
      showMap: false,
      userFormData: [
        {
          firstName: "",
          lastName: "",
          email: "",
          phone: "",
          address: {
            street: "",
            neighborhood: "",
            city: "",
            state: "",
            country: ""
          }
        }
      ]
    };
  },

  methods: {
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(this.showPosition);
        this.showMap = true;
        console.log(this.currentLocation);
      }
    },

    showPosition(position) {
      this.currentLocation.lat = position.coords.latitude;
      this.currentLocation.lng = position.coords.longitude;
      this.$axios
        .$get(
          "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
            this.currentLocation.lat +
            "," +
            this.currentLocation.lng +
            "&key=AIzaSyAc_W5NukW2Uy-fAs6GA2YiJN9rbluyYpU"
        )
        .then(res => {
          this.userFormData[0].address.street =
            res.results[0].address_components[0].long_name;

          this.userFormData[0].address.neighborhood =
            res.results[0].address_components[1].long_name;

          this.userFormData[0].address.city =
            res.results[0].address_components[2].long_name;

          this.userFormData[0].address.state =
            res.results[0].address_components[3].long_name;

          this.userFormData[0].address.country =
            res.results[0].address_components[4].long_name;
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>

<style scoped>
GmapMap {
  width: 100%;
}
</style>