<template>
  <DiscordMessage />
</template>

<script>
import DiscordMessage from "./components/DiscordMessage.vue";

export default {
  name: "App",
  components: {
    DiscordMessage,
  },
};
</script>

<style>
body {
  color: white;
  background-color: #212121;
}
</style>
