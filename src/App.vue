<template>
  <v-app light>
    <v-app-bar app>
      <v-toolbar-title>graph master's mania</v-toolbar-title>
      <v-spacer></v-spacer>
     
        <v-tabs>
          <v-tab to="/">Home</v-tab>
          <v-tab to="/page-01">Page 01</v-tab>
          <v-tab to="/page-02">Page 02</v-tab>

        </v-tabs>


    </v-app-bar>
    <router-view />
  </v-app>
</template>

<script setup>
  //
</script>
