<template>
  <div>
    <div style="margin-bottom: 1rem;">
      <h4>Kategorier:</h4>
      <label v-for="category in categories" :key="category">
        <input type="checkbox" :value="category" v-model="selectedCategories" />{{ category }} 
      </label>
    </div>
    <hr>
    <p v-if="selectedCategory">Kategori: <span style="font-weight: bold;">{{ selectedCategory }}</span></p>
    <p v-if="selectedItem" style="font-size: 1.5rem;">{{ selectedItem.name }}</p>
    <p v-if="!remainingItems.length && usedItems.length > 0">Det var den sidste i de valgte kategorier</p>
    <div style="position: absolute; bottom: 0; margin-bottom: 2rem; left: 50%; transform: translateX(-50%);">
      <button @click="pickRandomItem" style="width:100%; margin-bottom: 20px;">Næste spørgsmål</button>
      <button @click="goBack" :disabled="!previousItem" style="width:100%">Tilbage</button>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue';
import itemsData from '../assets/items.json';

export default {
  data() {
    return {
      selectedItem: null,
      selectedCategory: null,
      previousItem: null,
      items: ref([]),
      usedItems: [],
      categories: [],
      selectedCategories: [],
    };
  },
  mounted() {
    this.items = ref(itemsData.items);
    this.categories = Array.from(new Set(itemsData.items.map(item => item.category)));
  },
  computed: {
    remainingItems() {
      if (this.selectedCategories.length > 0) {
        return this.items.filter(item => !this.usedItems.includes(item) && this.selectedCategories.includes(item?.category));
      } else {
        return this.items.filter(item => !this.usedItems.includes(item));
      }
    },
  },
  methods: {
    pickRandomItem() {
      if (this.usedItems.length === this.items.length) {
        this.usedItems = [];
      }

      if (this.remainingItems.length === 0) {
        this.usedItems = [];
        this.previousItem = null;
      }

      const randomIndex = Math.floor(Math.random() * this.remainingItems.length);
      this.previousItem = this.selectedItem;
      this.selectedItem = this.remainingItems[randomIndex];
      this.selectedCategory = this.selectedItem?.category;
      this.usedItems.push(this.selectedItem);
    },
    goBack() {
      if (this.previousItem) {
        this.usedItems.pop();
        this.selectedItem = this.previousItem;
        this.selectedCategory = this.selectedItem?.category;
        this.previousItem = null;
      }
    },
  },
};
</script>