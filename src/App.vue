<template>
  <div class="container mx-auto max-w-[1440px] min-h-full h-screen p-4 2xl:p-0" >
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-10 w-full my-24" >
      <template v-for="(menu, menuIndex) in menus" :key="menuIndex">
         <Menu
          :group="menuIndex"
          :items="menu"
          :rules="currentActiveRules"
          :selectedItems="selecteditemsByGroup"
          @item:selected="handleSelectedItem($event)"
          @item:unselect="handleUnselectedItem($event)"
        />
      </template>

      <div class="lg:col-span-3 py-10 text-center" >
        <div v-if="showSelectedItems" class="selected-items" >
          {{ currentSelectedItems }}
        </div>

        <button
          class="btn"
          :class="{ 'btn--success': submittable }"
          @click="sumbmit($event)"
          :disabled="!submittable"
        >
          Submit selection
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, defineComponent } from 'vue'
import { map, find, some, findIndex } from 'lodash'

import Menu from './components/Menu.vue'

defineComponent({ Menu }) 

const showSelectedItems = ref(false);
const currentActiveGroup = ref(0);
const selecteditemsByGroup= ref([]);

const menus= [
  [
    { id: '101', value: 'Vegetarian' },
    { id: '102', value: 'Nut allergy' },
    { id: '103', value: 'Halal' }
  ],
  [
    { id: '201', value: 'Cashew chicken' },
    { id: '202', value: 'Sweet and sour pork' },
    { id: '203', value: 'Stir fried Tofu' },
    { id: '204', value: 'Vegetable fried rice' },
    { id: '205', value: 'Pad Thai' },
    { id: '206', value: 'Massaman beef' },
  ],
  [ 
    { id: '301', value: 'Peanut sauce' },
    { id: '302', value: 'Oyster sauce' },
    { id: '303', value: 'Vegetable spring rolls' },
    { id: '304', value: 'Steamed rice' },
  ],
]

const rules = {
  // 'Vegetarian' is NOT compatible with 'Cashew chicken', 'Sweet and sour pork', 'Massaman beef', 'Oyster sauce'
  101: [201, 202, 206, 302], 
  // 'Nut allergy' is NOT compatible with 'Cashew chicken', 'Peanut sauce',
  102: [201, 301], 
  // 'Halal' is NOT compatible with 'Sweet and sour pork',
  103: [202], 
  // 'Vegetable fried rice' is NOT compatible with 'Steamed rice' (you don't need more rice... carb overload),
  204: [304],
  // 'Pad thai' is NOT compatible with 'Steamed rice' (Pad thai comes with noodles),
  205: [304],
}

const submittable = computed(() => {
  return selecteditemsByGroup.value.length === menus.length
})

const currentSelectedItems = computed(() => {
  return map(selecteditemsByGroup.value, (_item) => {
    const fromGroupIndex = findIndex(menus, menu => some(menu, item => item.id === _item))
    const item = find(menus[fromGroupIndex], item => item.id === _item)

    return item.value
  }).join(', ').replace(/, ([^,]*)$/, ' and $1')
})

const currentActiveRules = computed(() => {
  const _rules = map(selecteditemsByGroup.value, (_item) => {
    if (rules[_item]) {
      return rules[_item]
    }
  })

  return _rules.flat().filter(item => item !== undefined)
})

watch(selecteditemsByGroup.value, (newX) => {
  showSelectedItems.value = false
})

const handleSelectedItem = (_item) => {
  const fromGroupIndex = findIndex(menus, menu => some(menu, item => item.id === _item.id))

  if (!selecteditemsByGroup.value[fromGroupIndex]) {
    selecteditemsByGroup.value.push([])
  }

  if(selecteditemsByGroup.value[fromGroupIndex]) {
    selecteditemsByGroup.value[fromGroupIndex] = _item.id
  }

  currentActiveGroup.value = fromGroupIndex + 1
}

const handleUnselectedItem = (group) => {
  delete selecteditemsByGroup.value.splice(group, 1)
}

const sumbmit = (event) => {
  showSelectedItems.value = true
}
</script>
