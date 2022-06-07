<template>
  <div class="menu">
    <h1 class="menu__title"> Group {{ group + 1 }} </h1>
    <div class="menu__items">
      <template v-for="item in items" :key="item.id">
        <Item
          :item="item"
          :checked="selectedItem === item.id"
          :disabled="nonSelectable(item.id) || !isMenuActive"
          @click="selectItem(item)" 
        />
      </template>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, defineProps, defineComponent, toRefs } from 'vue'
import { includes } from 'lodash'

import Item from "./Item.vue";

defineComponent({ Item }) 

const emit = defineEmits(['item:selected', 'item:unselect'])

const props = defineProps({
  rules: [Object, String, Array],
  group: [Number, String],
  selectedItems: [Array],
  items: {
    type: Array,
    defaut: () => []
  }
})

const selectedItem = ref(0)

watch(() => props.rules, (rules) => {
  if ((rules.length && selectedItem.value && nonSelectable(selectedItem.value))) {
    emit('item:unselect', props.group)
  }
});

const isMenuActive = computed(() => {
  return !(props.selectedItems.length < props.group)
})

const nonSelectable = (itemId) => {
  return includes(props.rules, Number(itemId))
}

const selectItem = (item) => {
  if (!isMenuActive.value || nonSelectable(item.id)) {
    return
  }
  
  selectedItem.value = item.id
  emit('item:selected', item)
}
</script>