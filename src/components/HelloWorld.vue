<template>
  <div>
    <div>
      <h3>Select Categories:</h3>
      <label v-for="category in categories" :key="category">
        <input type="checkbox" :value="category" v-model="selectedCategories" /> {{ category }}
      </label>
    </div>
    <button @click="pickRandomItem">Pick Random Item</button>
    <button @click="goBack" :disabled="!previousItem">Go Back</button>
    <p v-if="selectedItem">Selected Item: {{ selectedItem.name }}</p>
    <p v-if="selectedCategory">Category: {{ selectedCategory }}</p>
    <p v-if="!remainingItems.length && usedItems.length > 0">No more items in category</p>
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
