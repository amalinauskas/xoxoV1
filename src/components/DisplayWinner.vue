<template>
  <h2 class="text-6xl font-bold mb-8">
    Player <span class="text-yellow-500">{{ winner }}</span> wins
  </h2>
</template>

<script>
export default {
  name: "DisplayWinner",
  props: {
    winner: {
      type: String,
    },
  },
};
</script>
