<template>
  <div>
    <h3 class="text-xl mb-4">Player {{ player }} turn</h3>
    <div class="flex flex-col items-center mb-8">
      <div v-for="(row, x) in table" :key="x" class="flex">
        <div
          v-for="(cell, y) in row"
          :key="y"
          @click="move(x, y)"
          :class="`border border-black w-24 h-24 hover:bg-gray-200 duration-300 flex items-center justify-center material-icons-outlined text-4xl cursor-pointer ${
            cell === 'X' ? 'text-lime-400' : 'text-cyan-400'
          }`"
        >
          {{ cell === "X" ? "X" : cell === "O" ? "O" : "" }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "GameBoard",
  props: {
    player: {
      type: String,
    },
    table: {
      type: [Object, Boolean],
    },
  },
  methods: {
    move(x, y) {
      this.$emit("move", { x, y });
    },
  },
};
</script>
