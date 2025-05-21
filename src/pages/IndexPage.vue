<template>
  <q-page class="flex flex-center">
    <!-- <StartOne /> -->
    <!-- <ThreeScene /> -->
    <Earth />

  </q-page>
</template>

<script>
import { defineComponent } from "vue";
import ThreeScene from "src/components/ThreeScene.vue";
import StartOne from "src/components/StartOne.vue";
import Earth from "src/components/Earth.vue";

export default defineComponent({
  name: "IndexPage",
  components: {
    StartOne,
    ThreeScene,
    Earth
  },
});
</script>
