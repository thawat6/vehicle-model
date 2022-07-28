<template>
  <div>
    <v-card class="pa-3 mx-auto elevation-0" max-width="auto">
      <loading ref="loading" />

      <v-card-title
        ><h4 v-if="!vehicle">สร้างรุ่นรถ</h4>
        <h4 v-if="vehicle">แก้ไขรุ่นรถ</h4>
      </v-card-title>
      <v-divider></v-divider>

      <v-card-text>
        <v-container>
          <v-form ref="form1" v-model="valid">
            <v-row>
              <v-col cols="12" sm="6">
                <v-autocomplete
                  v-model="brandId"
                  :items="itemsBrand"
                  item-text="brandName"
                  item-value="brandId"
                  label="รหัส(ระบบ)ยี่ห้อ**"
                  :rules="[rules.required]"
                ></v-autocomplete>
              </v-col>
              <v-col cols="12" sm="6">
                <v-text-field
                  v-model="modelCode"
                  label="รหัสรุ่นรถ*"
                  :rules="[rules.required, rules.max20]"
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="name"
                  label="ชื่อรุ่นรถ*"
                  :rules="[rules.required, rules.max45]"
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-select
                  v-model="modelYear"
                  :items="itemsYear"
                  label="ปี"
                ></v-select>
              </v-col>

              <v-col cols="12" sm="6" md="4">
                <v-select
                  v-model="fuel"
                  :items="itemsFuel"
                  item-text="name"
                  item-value="value"
                  label="การใช้น้ำมัน"
                  :rules="[rules.required]"
                ></v-select>
              </v-col>

              <v-col cols="12" class="text-right">
                <v-btn class="mr-3" color="error" :to="`/`"> ยกเลิก </v-btn>
                <v-btn color="primary" v-if="!vehicle" @click="createVehicle">
                  บันทึก
                </v-btn>
                <v-btn color="primary" v-if="vehicle" @click="updateVehicle">
                  บันทึก
                </v-btn>
              </v-col>
            </v-row>
          </v-form>
        </v-container>
        <small>*สำหรับใช้งานภายใน</small>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import { HTTP } from "@/axios.js";
import loading from "./loading.vue";
import Vue from "vue";
import VueToast from "vue-toast-notification";
import "vue-toast-notification/dist/theme-sugar.css";

Vue.use(VueToast);

export default {
  components: { loading },
  props: ["brandId", "modelCode", "name", "modelYear", "fuel", "vehicle"],
  data() {
    return {
      itemsBrand: [],
      itemsFuel: [
        { name: "ดีเซล", value: "D" },
        { name: "เบนซิน", value: "S" },
      ],
      rules: {
        required: (value) => !!value || "จำเป็นต้องระบุ",
        max20: (v) => v.length <= 20 || "ไม่เกิน 20 ตัวอักษร",
        max45: (v) => v.length <= 45 || "ไม่เกิน 45 ตัวอักษร",
      },
      valid: true,
    };
  },
  computed: {
    itemsYear() {
      let start_year = new Date().getFullYear();
      let list = [];

      for (var i = start_year; i > start_year - 20; i--) {
        list.push(i);
      }
      return list;
    },
  },
  mounted() {
    this.getBrand();
  },
  methods: {
    getBrand() {
      HTTP.get(`/vehicle-model/brand`).then((response) => {
        this.itemsBrand = response.data;
      });
    },
    createVehicle() {
      this.$refs.form1.validate();
      if (this.valid) {
        let data = {
          brandId: this.brandId,
          modelCode: this.modelCode,
          name: this.name,
          modelYear: this.modelYear,
          fuel: this.fuel,
        };
        this.$refs.loading.start();
        HTTP.post(`/vehicle-model/store`, data)
          .then((response) => {
            if (response.status == 201) {
              this.$router.push(`/`);
              Vue.$toast.success("สร้างรุ่นรถแล้ว", {
                position: "bottom",
              });
            } else {
              this.$refs.loading.finish();
              Vue.$toast.error("สร้างรุ่นรถไม่สำเร็จ", {
                position: "bottom",
              });
            }
          })
          .catch((err) => {
            this.$refs.loading.finish();
            Vue.$toast.error(err, {
              position: "bottom",
            });
          });
      }
    },
    updateVehicle() {
      this.$refs.form1.validate();
      if (this.valid) {
        this.$refs.form1.validate();
        if (this.valid) {
          let data = {
            modelId: this.vehicle.modelId,
            brandId: this.brandId,
            modelCode: this.modelCode,
            name: this.name,
            modelYear: this.modelYear,
            fuel: this.fuel,
          };
          this.$refs.loading.start();
          HTTP.put(`/vehicle-model/update`, data)
            .then((response) => {
              if (response.status == 200) {
                this.$router.push(`/`);
                Vue.$toast.success("อัพเดตรุ่นรถแล้ว", {
                  position: "bottom",
                });
              } else {
                this.$refs.loading.finish();
                Vue.$toast.error("อัพเดตรุ่นรถไม่สำเร็จ", {
                  position: "bottom",
                });
              }
            })
            .catch((err) => {
              this.$refs.loading.finish();
              Vue.$toast.error(err, {
                position: "bottom",
              });
            });
        }
      }
    },
  },
};
</script>

<style>
</style>