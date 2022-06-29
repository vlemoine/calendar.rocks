<template>
  <NuxtLayout>
    <!-- Color mode: {{ $colorMode.value }} -->
    <Month :date="date"></Month>
  </NuxtLayout>
</template>

<script lang="ts" setup>
  const date: object = new Date();
</script>

<style>
html, body, #__nuxt {
  height: 100%;
}
body {
  background-color: #fff;
  color: rgba(0,0,0,0.8);
}
.dark-mode body {
  background-color: #222;
  color: #fff;
}
</style>