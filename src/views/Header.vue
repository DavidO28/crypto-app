<template>
  <header
    class="flex flex-row justify-between p-5 items-center border-b-amber-600 border-b-8"
  >
    <div class="flex flex-row items-center">
      <img src="../assets/crypto.png" alt="btc logo" class="size-16 me-3" />
    </div>
    <div class="relative">
      <input
        type="text"
        class="textbox"
        placeholder="Search"
        v-model="localSearchText"
        @input="emitSearch"
      />
      <button
        v-if="localSearchText"
        @click="clearSearchInput"
        class="clear-input flex flex-row justify-center items-center absolute right-3 top-3"
      >
        &#x2715;
      </button>
    </div>
  </header>
</template>

<script setup lang="ts">
import { ref, watch } from "vue";

const props = defineProps({
  searchText: { type: String },
});

const emit = defineEmits(["update:searchText"]);

const localSearchText = ref(props.searchText);

const emitSearch = () => {
  emit("update:searchText", localSearchText.value!);
};

const clearSearchInput = () => {
  localSearchText.value! = "";
};

watch(localSearchText, (newValue) => {
  emit("update:searchText", newValue!);
});
</script>

<style scoped>
.textbox {
  width: 200px;
  padding: 10px;
  background-color: rgba(255, 255, 255, 0.8);
  color: #212121;
  border: 1px solid black;
  border-radius: 5px;
}

.textbox:focus {
  outline: 0;
  background-color: #fff;
}

.clear-input {
  height: 20px;
  width: 20px;
  border-radius: 100%;
  background-color: red;
  color: black;
}
</style>
