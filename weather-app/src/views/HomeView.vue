<script setup>
import { ref } from 'vue';
import axios from 'axios';

const searchQuery = ref("");
const queryTimeout = ref(null);
const mapAPIKEY = "Z1wSWDHus9R2befBxrFH";
const mapSearchResults = ref(null);
const searchError = ref(null);

const GetSearchresults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {

      try {
        const url = `https://api.maptiler.com/geocoding/${searchQuery.value}.json?autocomplete=false&fuzzyMatch=true&limit=4&key=${mapAPIKEY}`;
        const result = await axios.get(url);
        mapSearchResults.value = result.data.features;
      }
      catch {
        searchError.value = true;
      }
      return;
    }
    mapSearchResults.value = null;
  }, 500);
};
</script>

<template>
  <main class="container text-white ">
    <div class="pt-4 mb-8 relative">
      <input @input="GetSearchresults" type="text" placeholder="search for a city or a state" class="py-2 px-1 w-full bg-transparent border-b
        focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]" v-model="searchQuery" />

      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapSearchResults">
        <p v-if="searchError">
          Sorry, something went wrong in the search, please try again.
        </p>
        <p v-if="!searchError && mapSearchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li v-for="searchResult in mapSearchResults" :key="searchResult.id" class="py-2 cursor-pointer">
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>
