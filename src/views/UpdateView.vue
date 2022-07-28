<template>
  <div>
    <from-vehicle-model
      :brandId="brandId"
      :modelCode="modelCode"
      :name="name"
      :modelYear="modelYear"
      :fuel="fuel"
      :vehicle="vehicle"
    />
  </div>
</template>

<script>
import { HTTP } from "@/axios.js";
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
  computed: {
    vehicleId() {
      return this.$route.query.id;
    },
  },
  async mounted() {
    this.showVehicle();
  },
  methods: {
    showVehicle() {
      HTTP.get(`/vehicle-model/show?modelId=${this.vehicleId}`).then(
        (response) => {
          if (response.data) {
            this.vehicle = response.data.data;
            this.brandId = response.data.data.brandId;
            this.modelCode = response.data.data.modelCode;
            this.name = response.data.data.name;
            this.modelYear = response.data.data.modelYear;
            this.fuel = response.data.data.fuel;
          }
        }
      );
    },
  },
};
</script>

<style>
</style>