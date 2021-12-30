<template>
  <div class="relative h-full min-h-screen">
   

    <!--body-->
    <div class="p-10">
      <router-view />
    </div>



    <!-- no mobile -->
    <TheMobileCover v-if="windowWidth < 800" />
  </div>
</template>


<style>
* {
  font-family: 'Source Sans Pro', monospace;
}
input[type='radio']:checked + span {
  @apply text-black;
}
</style>
