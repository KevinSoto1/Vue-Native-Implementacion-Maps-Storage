<template>
  <app-navigator></app-navigator>
</template>

<script>
import {
  createAppContainer,
  createMaterialTopTabNavigator,
  createStackNavigator
} from "vue-native-router";

import GeolocationScreen from "./screens/Geolocation.vue";
import DatabaseScreen from "./screens/Database.vue";
const Tabs = createMaterialTopTabNavigator({

  Map: GeolocationScreen,
  DB: DatabaseScreen
});
const StackNavigator = createStackNavigator(
  {
    Tabs,
    Map: GeolocationScreen
  },
  {
    defaultNavigationOptions: {
      gestureEnabled: false,
      title: "AppImpl",
      headerTitleStyle: {
        textAlign: "center",
        alignSelf: "center",
        color: "white"
      },
      headerStyle: {
        backgroundColor: "#0288D1"
      }
    }
  }
);
const AppNavigator = createAppContainer(StackNavigator);
export default {
  components: { AppNavigator }
};
</script>