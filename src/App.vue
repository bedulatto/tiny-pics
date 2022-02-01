<template>
  <v-app>
    <v-main>
      <TinyPicGenerator />
    </v-main>
  </v-app>
</template>

<script>
import TinyPicGenerator from "./components/TinyPicGenerator";
export default {
  name: "App",
  components: {
    TinyPicGenerator,
  },
};
</script>

