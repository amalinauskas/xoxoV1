<template>
  <div>
    <p>
      {{ twoot.move }}
    </p>
  </div>
</template>

<script>
export default {
  name: "DisplayMoves",
  props: {
    twoot: {
      type: Object,
    },
  },
};
</script>
