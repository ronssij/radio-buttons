<template>
  <div
    class="menu__item"
    :class="{ 'item--checked': checked, 'item--disabled': disabled }"
  >
    <span class="item__id"> {{ item.id }} </span>
    <div class="item__title">
      {{ item.value }}
    </div>

    <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none">
      <circle cx="12" cy="12" r="12" fill="#fff" fill-opacity="0.2"></circle>
      <path d="M7 13l3 3 7-7" stroke="#fff" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"></path>
    </svg>
  </div>
</template>

<script setup>
defineProps({
  checked: Boolean,
  disabled: Boolean,
  item: {
    type: Object,
    defaut: () => []
  }
})
</script>