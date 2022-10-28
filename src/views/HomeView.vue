<template>
    <main class="container text-white">
        <div class="pt-4 mb-8 relative">
            <input type="text" placeholder="Search for a city or state"
                   @input="getSearchResult"
                   v-model="searchQuery"
                   class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
            <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
                v-if="mapboxSearchResult">
                <p v-if="searchError">Sorry, something went wrong please try again</p>
                <p v-if="!searchError && mapboxSearchResult.length === 0">No results match your query, try a different
                    term.</p>
                <template v-else>
                    <li v-for="search in mapboxSearchResult" :key="search.id"
                        @click="previewCity(search)"
                        class="py-2 cursor-pointer">
                        {{ search.place_name }}
                    </li>
                </template>
            </ul>
        </div>
        <div class="flex flex-col gap-4">
            <Suspense>
                <CityList/>
                <template #fallback>
                    <CityCardSkeleton/>
                </template>
            </Suspense>
        </div>
    </main>
</template>

<script setup>
import CityCardSkeleton from "@/components/CityCardSkeleton.vue";
import CityList from "@/components/CityList.vue";
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

let router = useRouter();
const mapboxAPIKey = "pk.eyJ1IjoidGVtbXlrIiwiYSI6ImNsOXMxYmRoczB2azEzbnRmMnNoaHg5dGEifQ.o1BVDX79gP1JhwOAMweBpg"
let searchQuery = ref("")
const queryTimeout = ref(null)
let mapboxSearchResult = ref(null)
let searchError = ref(null)

let getSearchResult = () => {
    clearTimeout(queryTimeout.value)
    queryTimeout.value = setTimeout(async() => {
        if(searchQuery.value !== "") {
            try {
                const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
                mapboxSearchResult.value = result.data.features;
                console.log(mapboxSearchResult.value);
                return;
            } catch(e) {
                searchError.value = true
            }

        }
        mapboxSearchResult.value = null;
    }, 300)
}
let previewCity = (search) => {
    console.log(search);
    let [city, state] = search.place_name.split(",");
    console.log(city, state);
    router.push({
        name: 'cityView',
        params: {
            state: state.replaceAll(" ", ""),
            city: city
        },
        query: {
            lat: search.geometry.coordinates[1],
            lng: search.geometry.coordinates[0],
            preview: true,
        }
    })
}
</script>

<style scoped>

</style>
