<template>
  <div>
    <from-vehicle-model
      :brandId="brandId"
      :modelCode="modelCode"
      :name="name"
      :modelYear="modelYear"
      :fuel="fuel"
    />
  </div>
</template>

<script>
import FromVehicleModel from "../components/FromVehicleModel.vue";
export default {
  components: { FromVehicleModel },
  data() {
    return {
      brandId: null,
      modelCode: "",
      name: "",
      modelYear: null,
      fuel: "",
      vehicle: null,
    };
  },
};
</script>

<style>
</style>