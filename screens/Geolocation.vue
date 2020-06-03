<template>
  <view class="container">
    <text>Esta es tu dirección: {{ locationText }}</text>
    <text v-if="mark">{{ mark.latitude }},{{ mark.longitude }} </text>
   
    <touchable-opacity>
      <button title="Guardar Localización" @press="add"></button>
    </touchable-opacity>
    
    <MapView class="mapa" showsUserLocation :initial-region="coordinates" :onRegionChange="onRegionChange" :onPress="onPressMap" ref="mapa">
      
      <Marker
        v-if="mark"
        title="Tu localización"
        :coordinate="mark"
        draggable
      />
    </MapView>
  </view>
</template>

<script>
import * as Permissions from "expo-permissions";
import * as Location from "expo-location";
import { AsyncStorage } from "react-native";
import MapView, { Marker } from "react-native-maps";
import axios from "axios";
// https://github.com/react-native-community/react-native-maps
// https://github.com/react-native-community/react-native-maps/blob/master/docs/mapview.md
export default {
  data() {
    return {
      errorMessage: "",
      coordinates: {
        latitude: -0.1685769,
        longitude: -78.4849315,
        latitudeDelta: 0.1,
        longitudeDelta: 0.05
      },
      apiKey: "YOUR-API-KEY",
      mark: null,
      storageKey:null,
      listaLocal: [],
      text:"",
      locationText: ""
    };
  },
  mounted() {
    Permissions.askAsync(Permissions.LOCATION);
  },
  methods: {
    getLocation() {
      Permissions.askAsync(Permissions.LOCATION)
        .then(status => {
          if (status !== "granted") {
            this.errorMessage = "Permission to access location was denied";
          }
          Location.getCurrentPositionAsync({}).then(location => {
            // this.coordinates.latitude = location.coords.latitude;
            // this.coordinates.longitude = location.coords.longitude;
            console.log(this.coordinates);
            this.$refs.mapa.animateToRegion(
              {
                latitude: location.coords.latitude,
                longitude: location.coords.longitude,
                latitudeDelta: 0.001,
                longitudeDelta: 0.05
              },
              10
            );
            // console.log(this.location.coords.latitude);
          });
        })
        .catch(err => {
          console.log(err);
        });
    },
    onPressMap(event) {
        this.mark = event.nativeEvent.coordinate;
        axios
        .get(
          "https://geocode.xyz/" +
            this.mark.latitude +
            "," +
            this.mark.longitude +
            "?json=1&auth=" +
            this.apiKey
        )
        .then(response => {
          this.locationText = response.data.staddress;
          this.text=response.data.staddress
          // console.log(response.data.staddress);
        })
        .catch(error => {
          console.log(error);
        });

        this.storageKey=this.mark.latitude+','+this.mark.longitude;
        

    },
    async add() {
      try {
        if (this.listaLocal.includes(this.storageKey)) {
          alert("No puede ingresar lo mismo de nuevo");
        } else {
          await AsyncStorage.setItem(this.storageKey, this.text);
          alert(this.text + " agregado!");
          this.listaLocal.push(this.text);
          console.log(this.listaLocal);
          this.text = "";
        }
      } catch (error) {
        console.log(error.message);
      }
    },
    onRegionChange(region) {
      this.coordinates = region;
      
    },
    
  },
  components: { MapView, Marker }
};
</script>

<style>
.container {
  flex: 1;
  background-color: white;
  align-items: center;
  justify-content: center;
}
.mapa {
  height: 500px;
  width: 400px;
}
.text-color-primary {
  color: blue;
}
</style>