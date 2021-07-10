<template>
  <div class="flex flex-col h-screen max-h-screen">
    <!-- Search/ Reasult -->
    <div
      class="z-20 flex justify-center relative bg-hero-pattern bg-cover px-4 pt-8 pb-32"
    >
      <!-- search Input -->
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center text-3xl pb-4 font-semibold">
          IP Address Tracker
        </h1>
        <div class="flex">
          <input
            v-model="quryIp"
            class="flex-1 py-3 px-2  rounded-tl-md rounded-bl-md focus:outline-none"
            type="text"
            placeholder="Search for any IP addres or leave empty to get you IP info"
          />
          <i
            @click="getIpinfo"
            class="cursor-pointer bg-black text-white px-4  rounded-tr-md rounded-br-md items-center flex fas fa-chevron-right"
            aria-hidden="true"
          ></i>
        </div>
      </div>
      <!-- IP Info -->
      <IPInfo v-if="ipInfo" v-bind:ipInfo="ipInfo" />
    </div>

    <!-- map -->
    <div id="mapid" class="h-full z-10"></div>
  </div>
</template>

<script>
// @ is an alias to /src
import IPInfo from "../components/IPInfo.vue";
import leaflet from "leaflet";
import { onMounted, ref } from "@vue/runtime-core";
import axios from "axios";

export default {
  name: "Home",
  components: { IPInfo },
  setup() {
    let mymap;
    const quryIp = ref("");
    const ipInfo = ref(null);

    onMounted(() => {
      mymap = leaflet.map("mapid").setView([4.04549, 39.66644], 9);

      leaflet
        .tileLayer(
          "https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoiZmx1dHRlcmh1YiIsImEiOiJja3F4b3RnZW4xMGUzMm9xYTM0ZWxmY3lzIn0.97xEpPYBVC-QW-SgwL-8Pg",
          {
            attribution:
              'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
            maxZoom: 18,
            id: "mapbox/streets-v11",
            tileSize: 512,
            zoomOffset: -1,
            accessToken:
              "pk.eyJ1IjoiZmx1dHRlcmh1YiIsImEiOiJja3F4b3RnZW4xMGUzMm9xYTM0ZWxmY3lzIn0.97xEpPYBVC-QW-SgwL-8Pg",
          }
        )
        .addTo(mymap);
    });

    const getIpinfo = async () => {
      try {
        const data = await axios.get(
          `https://geo.ipify.org/api/v1?apiKey=at_CnCMVuwo4uuuCMlHheYs1emme4SS6&ipAddress=${quryIp.value}`
        );
        const result = data.data;
        ipInfo.value = {
          address: result.ip,
          state: result.location.region,
          timezone: result.location.timezone,
          isp: result.isp,
          lat: result.lat,
          lng: result.lng,
        };
        leaflet.marker([ipInfo.value.lat, ipInfo.value.lng]).addTo(mymap);
        mymap.setView([ipInfo.value.lat, ipInfo.value.lng], 13);
      } catch (err) {
        alert(err.massage);
      }
    };
    return { quryIp, ipInfo, getIpinfo };
  },
};
</script>
