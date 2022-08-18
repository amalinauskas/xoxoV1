<template>
  <button
    @click="resetDu()"
    class="px-4 py-2 bg-cyan-300 rounded uppercase font-bold hover:bg-cyan-500 duration-300"
  >
    Reset2
  </button>
</template>

<script>
export default {
  name: "ResetButton",
  methods: {
    resetDu() {
      this.$emit("reset");
    },
  },
};
</script>
