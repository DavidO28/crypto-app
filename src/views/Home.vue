<template>
  <Header v-model:searchText="searchText" :filteredCoins="filteredCoins" :titles="titles" />
  <div id="home" class="pt-28">
    <table class="table table-hover table-dark text-light">
      <thead>
        <tr>
          <th v-for="(title, index) in titles" :key="index">
            {{ title }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(coin, index) in filteredCoins" :key="coin.id">
          <td class="text-muted">{{ index }}</td>
          <td>
            <img :src="coin.image" :alt="coin.name" class="me-2" />
            <span>
              {{ coin.name }}
            </span>
            <span class="ms-2 text-muted text-uppercase">
              {{ coin.symbol }}
            </span>
          </td>
          <td>{{ coin.current_price.toLocaleString() }}</td>
          <td
            :class="[coin.price_change_percentage_24h > 0 ? 'text-success' : 'text-danger']"
          >
            {{ coin.price_change_percentage_24h }}
          </td>
          <td>{{ coin.total_volume.toLocaleString() }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, watch } from "vue";
import Header from "./Header.vue";
import type { Coin } from '@/types';



const coins = ref<Coin[]>([]);
const filteredCoins = ref<Coin[]>([]);
const titles = ["#", "Coin", "Price", "Price Change", "24h Volume"];
const searchText = ref<string>("");

let timeout: null | number;

watch(searchText, (newSearchValue) => {
  if (timeout) clearTimeout(timeout);
  timeout = setTimeout(() => {
    searchCoin(newSearchValue);
  }, 500);
});

const searchCoin = (searchValue: string) => {
  filteredCoins.value = coins.value.filter(
    (coin) =>
      coin.name.toLowerCase().includes(searchValue.toLowerCase()) ||
      coin.symbol.toLowerCase().includes(searchValue.toLowerCase())
  );
};

// onMounted(async () => {
//   const res = await fetch(
//     "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
//   );
//   const data = await res.json();
//   coins.value = data;
//   filteredCoins.value = data;
// });
</script>

<style scoped></style>
