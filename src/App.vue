<template>
  <router-view />
</template>

<script setup>
defineOptions({
  name: 'App'
});
</script>
<style>
.q-field--filled .q-field__control {
  background: #ffffff14 !important;
  color: white;
}

.q-field--filled .q-field__control input,
.q-field--filled .q-field__control i,
.q-field--filled .q-field__control .ellipsis,
.q-field--filled .q-field__control span {
  color: white !important;
}
</style>
