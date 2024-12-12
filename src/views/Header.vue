<template>
  <header
    class="flex flex-row justify-between p-5 items-center w-full fixed top-0 left-0 bg-#5b2c6f-800 border-b-amber-600 border-b-8"
  >
    <a href="#home">
      <img src="../assets/crypto.png" alt="btc logo" class="size-16" />
    </a>
    <input
      type="text"
      class="textbox"
      placeholder="Search"
      v-model="localSearchText"
      @input="emitSearch"
    />
  </header>
</template>

<script setup lang="ts">
import { ref, watch } from "vue";

const props = defineProps({
  searchText: { type: String },
});

const emit = defineEmits(["update:searchText"])

const localSearchText = ref(props.searchText);

const emitSearch = () => {
  emit("update:searchText", localSearchText.value!);
};

watch(localSearchText, (newValue) => {
  emit("update:searchText", newValue!);
});
</script>

<style scoped>
header {
  background-color: #5b2c6f;
}

.textbox {
  outline: 0;
  width: 200px;
  padding: 10px;
  background-color: rgba(255, 255, 255, 0.8);
  color: #212121;
  border: 0;
  border-radius: 5px;
}

.textbox:focus {
  outline: 0;
  background-color: #fff;
}
</style>
