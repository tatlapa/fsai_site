<script setup lang="ts">

definePageMeta({
  alias: ["/shop"],
  layout: "shop-menu-filter",
});
</script>

<template>
  <div class="grid grid-cols-6 gap-2"><ViewsShopCardItem /></div>
</template>
