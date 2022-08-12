<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar class="bg-secondary">
        <q-toolbar-title class="text-center text-weight-bold">
          Pokédex
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-footer elevated class="bg-secondary shadow-2 text-white fixed-bottom">
      <q-tabs
        indicator-color="primary"
        active-color="primary"
        class="text-white"
      >
        <q-tab name="home" icon="home" label="home" class="text-capitalize" no-caps></q-tab>
        <q-tab name="favorite" icon="favorite" label="favorite" class="text-capitalize" no-caps>
           <q-badge color="red" floating>4</q-badge>
        </q-tab>
        <q-tab name="notification" icon="notifications_active" label="note" class="text-capitalize" no-caps>
          <q-badge color="red" floating>2</q-badge>
        </q-tab>
        <q-tab name="event" icon="event" label="event" class="text-capitalize" no-caps>
           <q-badge color="red" floating>2</q-badge>
        </q-tab>
        <q-tab name="settings" icon="settings" label="settings" class="text-capitalize" no-caps></q-tab>

      </q-tabs>
    </q-footer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
export default ({
  name: 'MainLayout',

  setup () {

    return {}
  }
})
</script>
