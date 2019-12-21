<template>
  <v-app>
    <v-app-bar :clipped-left="clipped" fixed app dark color="primary">
      <v-icon dark>mdi-apps</v-icon>
      <v-toolbar-title style="font-weight: bold" v-text="title" />
      <v-spacer />
    </v-app-bar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-footer :fixed="fixed" app>
      <span>&copy; 2019</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      clipped: false,
      fixed: false,
      title: 'マンダラート'
    }
  }
}
</script>
