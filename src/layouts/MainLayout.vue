<template>
  <q-layout view="hhh lpR lfr">
    <q-header reveal elevated class="bg-primary text-white">
      <q-toolbar>
        <q-toolbar-title class="absolute-center">
          RegexMaker
          <small class="text-weight-thin">v1.0.0</small>
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>

    <q-footer bordered class="bg-grey-8 text-white">
      <q-toolbar>
        <q-toolbar-title class="li">
          RegexMaker
          <small class="text-weight-thin">v1.0.0</small>
        </q-toolbar-title>
      </q-toolbar>
    </q-footer>
  </q-layout>
</template>

<script>
export default {
  name: "MainLayout",

  data() {
    return {
      leftDrawerOpen: false
    };
  }
};
</script>
