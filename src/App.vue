<script setup lang="ts">

import Form from './components/Form.vue'

</script>

<template>
  <Form />
</template>

<style lang="scss">
html,
body {
  min-height: 100vh;
}
body {
  font-family: "DM Sans", sans-serif;
  background: linear-gradient(118.87deg, #ffffff -17.53%, #e0eaf7 102.01%);
  min-height: 100vh;
  margin: 0;
  padding: 20px;
  position: relative;
  -webkit-font-smoothing: antialiased;
  @media (min-width: 1024px) {
    padding: 0;
  }
}
* {
  box-sizing: border-box;
}
* {
  user-select: none;
  -webkit-tap-highlight-color: transparent;
}

*:focus {
  outline: none;
}
:root {
  --popper-theme-background-color-hover: #ffffff;
}
.form-wrapper {
  width: 874px;
  margin: 0 auto;
  background: #ffffff;
  box-shadow: 0px 40px 60px -33px rgba(0, 0, 0, 0.04);
  border-radius: 25px;
  padding: 94px 106px;
}
</style>
