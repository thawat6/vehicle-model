<template>
  <div class="pa-10">
    <list-vehicle-model />
  </div>
</template>

<script>
import ListVehicleModel from "../components/ListVehicleModel.vue";

export default {
  name: "Home",

  components: {
    ListVehicleModel,
  },
};
</script>
