<script setup lang="ts">
import { ref } from "vue";
import { useI18n } from "vue-i18n";

let { locale } = useI18n();

let showMenuBar = ref(true);
function toggleLanguage() {
  locale.value = locale.value == "hu" ? "en" : "hu";
}
</script>

<template>
  <q-layout view="hHh LpR fFf">
    <!-- Fejléc -->
    <q-header v-model="showMenuBar" class="bg-primary text-white" elevated>
      <q-toolbar>
        <q-toolbar-title>
          <q-avatar>
            <q-img src="../assets/Jedlik_small.png" />
          </q-avatar>
          BNL-FL Dolgozat
        </q-toolbar-title>

        <q-tabs v-if="$q.screen.gt.sm" inline-label shrink>
          <q-route-tab icon="mdi-home" label="Home" no-caps to="/" />
          <q-route-tab icon="mdi-page-layout-body" label="Empty" no-caps to="/empty" />
          <q-route-tab icon="mdi-school-outline" label="Basic" no-caps to="/basic" />
          <q-route-tab icon="mdi-table" label="qTable" no-caps to="/table" />
        </q-tabs>
        <q-btn flat icon="mdi-comment-text-multiple" @click="toggleLanguage">
          <q-badge color="red" floating :label="locale" />
        </q-btn>
        <q-btn flat icon="mdi-theme-light-dark" @click="$q.dark.toggle" />
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
