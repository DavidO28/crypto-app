<template>
  <Header v-model:searchText="searchText" />
  <div class="overflow-x-auto mt-5">
    <table class="m-auto w-max">
      <thead>
        <tr>
          <th v-for="(title, index) in titles" :key="index" class="px-4 py-2">
            {{ title }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(coin, index) in paginatedCoins"
          :key="coin.id"
          class="text-center border-b-amber-600 border-b-2"
        >
          <td class="px-4 py-2">
            {{ index + 1 + (currentPage - 1) * coinsPerPage }}
          </td>
          <td class="flex justify-start items-center text-center px-4 py-2">
            <img :src="coin.image" :alt="coin.name" class="size-8 me-3" />
            <span class="truncate" style="max-width: 200px">{{ coin.name }}</span>
          </td>
          <td class="px-4 py-2">
            {{
              coin.current_price < 0.01
                ? coin.current_price.toFixed(8)
                : coin.current_price.toLocaleString()
            }}
          </td>

          <td
            :class="[
              coin.price_change_percentage_24h > 0
                ? 'text-green-500'
                : 'text-red-500',
              'px-4 py-2',
            ]"
          >
            {{ coin.price_change_percentage_24h }}
          </td>
          <td class="px-4 py-2">{{ coin.total_volume.toLocaleString() }}</td>
        </tr>
      </tbody>
    </table>
  </div>

  <div class="flex justify-center my-5">
    <button
      :disabled="currentPage === 1"
      @click="changePage(currentPage - 1)"
      class="px-4 py-2 mx-2 bg-sky-600 rounded-full text-white"
    >
      &#8592;
    </button>
    <span class="px-4 py-2">Page {{ currentPage }}/{{ totalPages }}</span>
    <button
      :disabled="currentPage === totalPages"
      @click="changePage(currentPage + 1)"
      class="px-4 py-2 mx-2 bg-sky-600 rounded-full text-white"
    >
      &#8594;
    </button>
  </div>
</template>


<script setup lang="ts">
import { ref, watch, computed, onMounted } from "vue";
import Header from "./Header.vue";
import type { Coin } from "@/types";

const coins = ref<Coin[]>([]);
const filteredCoins = ref<Coin[]>([]);
const titles = ["#", "Coin", "Price", "Price Change", "24h Volume"];
const searchText = ref<string>("");
const coinsPerPage = 25;
const currentPage = ref(1);

const totalPages = computed(() => {
  return Math.ceil(filteredCoins.value.length / coinsPerPage);
});

const paginatedCoins = computed(() => {
  const start = (currentPage.value - 1) * coinsPerPage;
  const end = start + coinsPerPage;
  return filteredCoins.value.slice(start, end);
});

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
  currentPage.value = 1;
};

const scrollToTop = () => {
  window.scrollTo({ top: 0 });
};

const changePage = (page: number) => {
  if (page < 1 || page > totalPages.value) return;
  currentPage.value = page;
  scrollToTop();
};

onMounted(async () => {
  try {
    const res = await fetch(
      "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
    );

    if (!res.ok) {
      throw new Error(`Error: ${res.status} ${res.statusText}`);
    }

    const data = await res.json();
    coins.value = data;
    filteredCoins.value = data;
  } catch (error) {
    console.error("Failed to fetch data:", error);
  }
});
</script>