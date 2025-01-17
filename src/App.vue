<script setup>
import { RouterLink, RouterView } from 'vue-router';


</script>

<template>

  <nav class=" bg-green-300 p-6 z-auto fixed w-full">
    <div class="flex justify-end ">
      <RouterLink class="m-2" to="/">Home</RouterLink>
      <RouterLink class="m-2" to="/personajes">Personajes</RouterLink>
    </div>
  </nav>
  <RouterView></RouterView>
</template>