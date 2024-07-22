<template>
    <div>
      <div @click="toggle" :style="{ backgroundColor: backgroundColor, paddingLeft: depth * 20 + 'px' }">
        <span v-if="hasChildren">{{ isOpen ? '▼' : '▶' }}</span>
        {{ item.title }}
      </div>
      <div v-if="isOpen">
        <NodeItem
          v-for="child in children"
          :key="child.id"
          :item="child"
          :allItems="allItems"
          :depth="depth + 1"
        />
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref, computed, onMounted } from 'vue';
  
  interface Item {
    title: string;
    parent_id: string | null;
    id: string;
  }
  
  export default defineComponent({
    name: 'NodeItem',
    props: {
      item: {
        type: Object as () => Item,
        required: true,
      },
      allItems: {
        type: Array as () => Item[],
        required: true,
      },
      depth: {
        type: Number,
        required: true,
      },
    },
    setup(props) {
      const isOpen = ref(false);
  
      const children = computed(() => {
        return props.allItems.filter(child => child.parent_id === props.item.id);
      });
  
      const toggle = () => {
        isOpen.value = !isOpen.value;
        saveState();
      };
  
      const hasChildren = computed(() => {
        return children.value.length > 0;
      });
  
      const backgroundColor = computed(() => {
        return props.depth % 2 === 0 ? '#f0f0f0' : '#ffffff';
      });
  
      const saveState = () => {
        const expandedItems = JSON.parse(localStorage.getItem('expandedItems') || '[]');
        if (isOpen.value) {
          if (!expandedItems.includes(props.item.id)) {
            expandedItems.push(props.item.id);
          }
        } else {
          const index = expandedItems.indexOf(props.item.id);
          if (index > -1) {
            expandedItems.splice(index, 1);
          }
        }
        localStorage.setItem('expandedItems', JSON.stringify(expandedItems));
      };
  
      const loadState = () => {
        const expandedItems = JSON.parse(localStorage.getItem('expandedItems') || '[]');
        isOpen.value = expandedItems.includes(props.item.id);
      };
  
      onMounted(() => {
        loadState();
      });
  
      return {
        isOpen,
        children,
        toggle,
        hasChildren,
        backgroundColor,
      };
    },
  });
  </script>
  