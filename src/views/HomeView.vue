<template>
  <section class="font-nunito relative">
    <div class="min-h-screen flex items-center justify-center relative">
      <div class="absolute top-0 left-0 z-1 h-full w-full">
        <img src="../assets/imgs/header-bg.jpg" class="absolute top-0 left-0 h-full w-full object-cover" />
      </div>

      <div class="content absolute top-0 left-0 w-full h-full flex flex-col justify-center items-center text-center z-10">
        <h3 class="text-white text-6xl uppercase text-shadow">go, find, explore</h3>
        <p class="text-white text-3xl py-2">the essence of true discovery</p>

        <div class="absolute formulario content bottom-1/4 text-xl bg-backgroundForm p-10 z-100 rounded-md">
          <form action="" class="form-horizontal flex space-x-5">
            <div class="relative">
              <AutocompleteInput @suggestion-selected="destination_selected" />
            </div>
            <input type="date" v-model="checkin"
              class="form-control bottom-0 left-0 z-10 max-h-48 overflow-y-auto bg-white border border-gray-300 rounded-md p-2"
              placeholder="Check-in" />
            <input type="date" v-model="checkout"
              class="form-control bottom-0 left-0 z-10 max-h-48 overflow-y-auto bg-white border border-gray-300 rounded-md p-2"
              placeholder="Check-out" />
            <input type="button" class="w-fit p-3 rounded-md bg-blue-300 text-center text-white cursor-pointer"
              value="Search" @click="search_hotels" />
          </form>
        </div>
      </div>
    </div>
  </section>
  <section>
    <div class="inline-table p-3" v-for="hotel in hotels" :key="hotel.id">
      <Card :hotel="hotel" :checkin="checkin" :checkout="checkout" />
    </div>
  </section>
  <section>
    <Popular_Places />
  </section>
  <section>
    <Carousel />
  </section>
</template>

<script setup lang="ts">
import axios from "axios";
import { ref } from "vue";

import AutocompleteInput from "@/components/AutocompleteInput.vue";
import Card from "@/components/Card.vue";
import Carousel from "@/components/Carousel.vue";
import Popular_Places from "@/components/PopularPlaces.vue";

const city = ref("");
const country = ref("");
const checkin = ref("");
const checkout = ref("");
const hotels = ref<any[]>([]);
const search_hotels = () => {
  const checkinISO = new Date(checkin.value).toISOString();
  const checkoutISO = new Date(checkout.value).toISOString();
  axios
    .get(
      `/api/v1/properties/availability?checkin=${checkinISO}&checkout=${checkoutISO}&city=${city.value}&country=${country.value}`
    )
    .then((res) => {
      hotels.value = res.data;
    })
    .catch((err) => {
      console.error(err);
    });
};
const destination_selected = (data: { city: string; country: string }) => {
  city.value = data.city;
  country.value = data.country;
};
</script>

<style></style>
