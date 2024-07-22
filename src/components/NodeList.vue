<template>
    <div>
      <div v-for="item in rootItems" :key="item.id">
        <NodeItem :item="item" :allItems="items" :depth="0" />
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref, onMounted } from 'vue';
  import axios from 'axios';
  import NodeItem from './NodeItem.vue';
  
  interface Item {
    title: string;
    parent_id: string | null;
    id: string;
  }
  
  export default defineComponent({
    name: 'NodeList',
    components: {
      NodeItem,
    },
    setup() {
      const items = ref<Item[]>([]);
      const rootItems = ref<Item[]>([]);
  
      const fetchData = async () => {
        const response = await axios.get('https://64b4c8450efb99d862694609.mockapi.io/tree/items');
        items.value = response.data;
        rootItems.value = items.value.filter(item => item.parent_id === null);
      };
  
      onMounted(() => {
        fetchData();
      });
  
      return {
        items,
        rootItems
      };
    },
  });
  </script>
  
  <style scoped>
  button {
    margin-bottom: 10px;
  }
  </style>