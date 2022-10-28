<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"/>
    </div>
    <p v-if="savedCities.length === 0">No locations added. To start tracking a location, search in the field above</p>
</template>

<script setup>
import CityCard from "@/components/CityCard.vue";
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

let savedCities = ref([]);
let router = useRouter();
let getCities = async() => {
    if(localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
        let requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=7fca3d10caa65c3326ccd0835da8c394&units=imperial`))
        });

        let weatherData = await Promise.all(requests);

        //flicker Delay
        await new Promise((res) => setTimeout(res, 1000));

        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        })
    }
}

await getCities();
let goToCityView = (city) => {
    router.push({
        name: 'cityView',
        params: {
            state: city.state,
            city: city.city,
        },
        query: {
            id: city.id,
            lat: city.coords.lat,
            lng: city.coords.lng
        },
    });
}
</script>
