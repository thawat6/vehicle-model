<template>
  <div>
    <loading ref="loading" />
    <v-data-table
      :headers="headers"
      :items="vehicleModels"
      sort-by="calories"
      class="elevation-1"
      hide-default-footer
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>รายการรุ่นรถ</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-btn to="/create" color="primary" dark class="mb-2">
            สร้างรุ่นรถ
          </v-btn>

          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">ยืนยันการลบรุ่นรถนี้</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="error" text @click="closeDelete">ยกเลิก</v-btn>
                <v-btn dark color="blue darken-1" @click="deleteItemConfirm"
                  >ตกลง</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>

      <template v-slot:[`item.fuel`]="{ item }">
        <div v-if="item.fuel == 'D'">ดีเซล</div>
        <div v-else>เบนซิน</div>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
        <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="getVehicleModel()"> โหลดใหม่ </v-btn>
      </template>
    </v-data-table>
    <div class="text-center pt-5">
      <v-pagination
        v-model="page"
        :per-page="perPage"
        :records="records"
        :length="length"
        @input="getVehicleModel"
      ></v-pagination>
    </div>
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
  data: () => ({
    page: 1,
    perPage: 10,
    records: 0,
    vehicleId: null,
    dialogDelete: false,
    headers: [
      {
        text: "รหัสรุ่นรถ",
        align: "start",
        sortable: false,
        value: "modelCode",
      },
      { text: "ยี่ห้อ", value: "brandName" },
      { text: "ชื่อรุ่นรถ", value: "name" },
      { text: "ปีรถ", value: "modelYear" },
      { text: "การใช้น้ำมัน", value: "fuel" },
      { text: "Actions", value: "actions", sortable: false },
    ],

    vehicleModels: [],
  }),

  watch: {
    dialogDelete(val) {
      val || this.closeDelete();
    },
  },
  computed: {
    length: {
      get() {
        let records = this.records;
        let perpage = this.perPage;
        let length = records / perpage;
        if (length >= 1) {
          return Math.ceil(length);
        } else {
          return 1;
        }
      },
      set() {},
    },
  },

  created() {
    this.getVehicleModel();
  },

  methods: {
    getVehicleModel() {
      HTTP.get(`/vehicle-model?page=${this.page}&perPage=${this.perPage}`).then(
        (response) => {
          this.vehicleModels = response.data.data;
          this.records = response.data.total;
        }
      );
    },

    editItem(item) {
      this.$router.push(`/update/?id=${item.modelId}`);
    },

    deleteItem(item) {
      this.vehicleId = item.modelId;
      this.dialogDelete = true;
    },

    deleteItemConfirm() {
      this.$refs.loading.start();
      this.closeDelete();

      HTTP.delete(`/vehicle-model/delete?modelId=${this.vehicleId}`)
        .then((response) => {
          if (response.status == 200) {
            this.getVehicleModel();
            this.$refs.loading.finish();
            Vue.$toast.success("ลบรุ่นรถแล้ว", {
              position: "bottom",
            });
          } else {
            this.$refs.loading.finish();
            Vue.$toast.error("ลบรุ่นรถไม่สำเร็จ", {
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
    },

    closeDelete() {
      this.dialogDelete = false;
    },
  },
};
</script>

<style>
</style>