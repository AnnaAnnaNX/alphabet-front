<template>
  <div class="home">
    {{ state }}
    <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";
import { reactive } from '@vue/composition-api'

export default {
  name: "Home",
  components: {
    HelloWorld
  },
  setup() {
    let state = reactive({
      audioObject: null,
    });
    return { state };
  },
};
</script>
