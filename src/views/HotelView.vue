<template>
  <div>
    <section class="py-5 bg-gray-100 text-white text-center">
      <div class="relative w-screen h-96">
        <img
          src="https://images.pexels.com/photos/2869215/pexels-photo-2869215.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1"
          alt="Hotel image"
          class="w-full h-full object-cover backdrop-filter backdrop-blur-sm brightness-75"
        />
        <div
          class="absolute inset-0 flex flex-col items-center justify-center text-white"
        >
          <h2 class="text-5xl font-normal">{{ hotel.name }}</h2>
        </div>
      </div>
    </section>
    <section class="bg-gray-200 pb-5">
      <div class="container mx-auto grid grid-cols-2 gap-4">
        <div class="mt-8 flex flex-col items-center border-r-4">
          <h2 class="text-2xl pb-5 font-bold">Contact Details</h2>
          <p class="text-lg">
            Address: {{ hotel.address.street }}, {{ hotel.address.country }}
          </p>
          <p class="text-lg">Postal code: {{ hotel.address.postal_code }}</p>
          <p class="text-lg">Telephone: {{ hotel.phone }}</p>
        </div>
        <div class="mt-8 flex flex-col items-center">
          <h2 class="text-2xl pb-5 font-bold">Servicios</h2>
          <ul>
            <li v-for="hotelItem in hotel.amenities" class="text-center">
              {{ hotelItem.name }}
            </li>
          </ul>
        </div>
      </div>
    </section>
    <section>
      <div class="mt-8 text-center">
        <h2 class="text-2xl font-bold">Check-in</h2>
        <p class="text-lg">
          Check-in hours: {{ hotel.checkin.begin_time }} - {{ hotel.checkin.end_time }}
        </p>
        <p class="text-lg">Minimum age: {{ hotel.checkin.min_age }}</p>
      </div>
    </section>
    <section class="mt-8 text-center">
      <h2 class="text-3xl font-bold py-6">Our rooms</h2>
      <div class="flex justify-center flex-wrap">
        <CardRoom :room="room" v-for="room in rooms" />
      </div>
    </section>
    <Testimonies />
  </div>
</template>

<script setup lang="ts">
import axios from "axios";
import { onBeforeMount, ref } from "vue";
import { useRoute } from "vue-router";

import CardRoom from "@/components/CardRoom.vue";
import Testimonies from "@/components/Testimonies.vue";

const route = useRoute();
const hotel = ref<any>({});
const rooms = ref<any>([]);

onBeforeMount(async () => {
  const { data } = await axios.get(`/api/v1/properties/${route.params.id}`);
  hotel.value = data;

  data.rooms.forEach((room: any) => {
    room.bed_groups.forEach((bedgroup: any) => {
      bedgroup.configuration.forEach((bedgroupConfig: any) => {
        let { name } = room;
        if (bedgroup.description != '')
          name = `${name}, ${bedgroup.description}`;
        if (bedgroupConfig.type != '')
            name = `${name}, ${bedgroupConfig.type}`;

        rooms.value.push({
          id: room.id,
          name: name,
          property: data.id,
          quantity: bedgroupConfig.quantity,
          type: bedgroupConfig.type,
          price: bedgroupConfig.base_price,
          size: bedgroupConfig.size,
          amenities: room.amenities
        });
      });
    });
  });
});

</script>
