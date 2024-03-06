<script setup lang="ts">
import { ref } from "vue";


let showMenuBar = ref(true);

</script>

<template>
  <q-layout view="hHh LpR fFf">
    <!-- Fejléc -->
    <q-header v-model="showMenuBar" class="bg-primary text-white" elevated>
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar>
            <!-- <q-img src="../assets/Jedlik_small.png" /> -->
            <q-img src="https://media.tenor.com/2l4-h42qnmcAAAAi/toothless-dancing-toothless.gif"/>
          </q-avatar>
          BNH-FL Dolgozat
        </q-toolbar-title>      
     
      </q-toolbar>
    </q-header>
    <!-- A router ide (router-view) tölti be az oldalt -->
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<style lang="scss">
.active {
  background-color: #dddddd;
}
</style>
