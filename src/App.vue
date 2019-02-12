<template>
  <div id="app">
    <RwvHeader />
    <router-view></router-view>
    <RwvFooter />
    <Chat />
  </div>
</template>

<script>
import RwvHeader from "@/components/TheHeader";
import RwvFooter from "@/components/TheFooter";
import Chat from "@/components/Chat";

export default {
  name: "App",
  components: {
    RwvHeader,
    RwvFooter,
    Chat
  }
};
</script>

<style></style>
