<template>
  <view>
    <view class="container">
      <touchable-opacity class="button" :on-press="get">
        <text class="button-text">Cargar Datos</text>
      </touchable-opacity>
      <touchable-opacity class="button" :on-press="clearList">
        <text class="button-text">Eliminar Todo</text>
      </touchable-opacity>
    </view>
    <view class="test" v-for="(wea, index) in listaDir" :key="index">
      <touchable-opacity :on-press="() => remove(wea[0])">
        <text class="text-container">Dirección: {{ wea[1] }}</text>
        <text class="text-container">Coordenadas: {{ wea[0] }}</text>
      </touchable-opacity>
    </view>
  </view>
</template>

<script>
import { AsyncStorage } from "react-native";
export default {
  data() {
    return {
      text: "",
      listaWeas: [],
      listaDir: []
    };
  },
  mounted() {
    this.get();
  },
  methods: {
    
    async get() {
      try {
        const keys = await AsyncStorage.getAllKeys();
        const dir = await AsyncStorage.multiGet(keys)
        // const items = await AsyncStorage.multiGet(keys);
        this.listaWeas = keys;
        this.listaDir = dir;
        // console.log(this.listaVainas);
      } catch (error) {
        console.log(error.message);
      }
    },
    async remove(wea) {
      try {
        await AsyncStorage.removeItem(wea);
        this.listaWeas = [];
        this.get();
      } catch (error) {
        console.log(error.message);
      }
    },
    async clearList() {
      try {
        await AsyncStorage.clear();
        this.listaWeas = [];
        alert("Datos borrados");
      } catch (error) {
        console.log(error.message);
      }
    }
  }
};
</script>

<style>
.container {
  background-color: #333;
  flex-direction: row;
  padding: 20px;
}
.test {
  align-items: center;
  justify-content: center;
  padding-top: 10;
}
.text-input {
  background-color: white;
  margin-right: 5px;
  flex: 3;
}
.text-container {
  color: black;
  padding: 2;
}
.button {
  flex: 1;
  background-color: #008080;
  margin-left: 5px;
  align-items: center;
  justify-content: center;
  padding-top: 10px;
  padding-bottom: 10px;
}
.button-text {
  color: white;
  font-weight: normal;
}
</style>