<template><h1>Home</h1></template>

<script>
  export default {
    name: "Dashboard",
    created() {
      console.log(process.env.VUE_APP_WEATHER_API_KEY);
    },
  };
</script>
