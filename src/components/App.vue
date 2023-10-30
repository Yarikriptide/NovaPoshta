<template>
  <div class="example">
    <div class="block">
      <p class="text">Пошук за назвою міста:</p>
      <input v-model="citySearch" class="search" @input="filterCities" placeholder="Пошук міста" />
    </div>
    <div class="block">
      <p class="text">Місто:</p>
      <select v-model="city" @change="selectWarehouses" class="select">
        <option value="" disabled selected>Населений пункт</option>
        <option v-for="c in filteredCities" :key="c.CityID">{{ c.Description }}</option>
      </select>
    </div>
    <div class="block">
      <p class="text">Поштове відділення:</p>
      <select v-model="warehouse" class="select">
        <option disabled selected value="">Відділення</option>
        <option v-for="w in warehouses" :key="w.Ref">{{ w.Description }}</option>
      </select>
    </div>
    <div class="block">
      <p class="text">Ви обрали: <br>
        Населений пункт: <span class="selected">{{ city }}</span> <br>
        Поштове відділення: <span class="selected">{{ warehouse }}</span>
      </p>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const cities = ref([]);
    const city = ref('');
    const warehouses = ref([]);
    const warehouse = ref('');
    const citySearch = ref('');

    const selectWarehouses = () => {
      axios.post("https://api.novaposhta.ua/v2.0/json/", {
        "apiKey": "36cc21acfa04b10c261dfb74c8ae9b31",
        "modelName": "Address",
        "calledMethod": "getWarehouses",
        "methodProperties": {
          "CityName": city.value
        }
      }).then(res => {
        warehouses.value = res.data.data;
      });
    };

    const filterCities = () => {
      // Фільтруємо список міст на основі введеного тексту
      const searchText = citySearch.value.toLowerCase();
      const filtered = cities.value.filter(c => c.Description.toLowerCase().includes(searchText));
      filteredCities.value = filtered;
    };

    onMounted(() => {
      axios.post("https://api.novaposhta.ua/v2.0/json/", {
        "apiKey": "36cc21acfa04b10c261dfb74c8ae9b31",
        "modelName": "Address",
        "calledMethod": "getCities",
        "methodProperties": {}
      }).then(res => {
        cities.value = res.data.data;
      });
    });

    const filteredCities = ref([]);

    return {
      cities,
      city,
      warehouses,
      warehouse,
      selectWarehouses,
      citySearch,
      filteredCities,
      filterCities,
    };
  }
};
</script>
