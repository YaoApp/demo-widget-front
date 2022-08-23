<script lang="ts">
import { defineComponent } from "vue";
export default defineComponent({
  props: {
    name: { type: String, required: true },
  },
});
</script>

<template>
  <input class="c-input" :name="name" :placeholder="name" />
</template>

<style scoped>
.c-input {
  width: 300px;
  height: 32px;
}
</style>
