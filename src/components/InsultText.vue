<template>
  <div>
    <v-skeleton-loader
      v-if="isShown"
      :loading="isLoading"
      transition="scale-transition"
      height="94"
      type="list-item-two-line"
    >
      <blockquote v-html="text" class="blockquote"></blockquote>
    </v-skeleton-loader>
  </div>
</template>

<script>
export default {
  name: "InsultText",
  data: () => ({}),
  props: ["isLoading", "isShown", "text"]
};
</script>

<style>
</style>