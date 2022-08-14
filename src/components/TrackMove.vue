<template>
  <h1>Moves Made</h1>
  <div v-for="move in moves" :key="move" class="move">
    <p>
      {{ move.move }}
    </p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      moves: [],
    };
  },

  methods: {
    async getData() {
      try {
        let response = await fetch("http://localhost:5000/moves");
        this.moves = await response.json();
      } catch (error) {
        console.log(error);
      }
    },
  },

  created() {
    this.getData();
  },
};
</script>
