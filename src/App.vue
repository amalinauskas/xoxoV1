<script>
const baseURL = "http://localhost:5000/moves";
export default {
  name: "App",
  data() {
    return {
      player: "X",
      table: false,
      gameId: 0,
      winner: false,
      gameFinished: false,
      moves: [],
    };
  },
  mounted() {
    this.resetTable();
    this.getData();
  },
  methods: {
    calculateWinner(squares) {
      const lines = [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6],
      ];
      for (let i = 0; i < lines.length; i++) {
        const [a, b, c] = lines[i];
        if (
          squares[a] &&
          squares[a] === squares[b] &&
          squares[a] === squares[c]
        ) {
          return squares[a];
        }
      }
      return null;
    },
    resetTable() {
      this.table = [
        ["", "", ""],
        ["", "", ""],
        ["", "", ""],
      ];
    },
    reset() {
      this.winner = false;
      this.gameId = 0;
      this.resetTable();
      this.gameFinished = false;
      this.moves = [];
    },
    move(x, y) {
      if (this.gameFinished) {
        return;
      }
      if (this.table[x][y] !== "") return;
      this.table[x][y] = this.player;
      this.player = this.player === "X" ? "O" : "X";
      this.postData();
    },
    async getData() {
      let lastGameId = sessionStorage.getItem("lastGameId");
      if (!lastGameId) {
        return;
      }

      try {
        let response = await fetch(
          `http://localhost:5000/moves?gameId=${lastGameId}`
        );
        let moves = await response.json();
        let lastMoves = moves[moves.length - 1];
        this.gameId = lastMoves.gameId;
        this.table = JSON.parse(lastMoves.move);
        this.moves = moves;
        console.log(this.moves);
      } catch (error) {}
    },

    async postData() {
      if (this.gameId === 0) {
        this.gameId = Date.now();
        sessionStorage.setItem("lastGameId", this.gameId);
      }
      const postData = {
        move: JSON.stringify(this.table),
        gameId: this.gameId,
      };
      try {
        const res = await fetch(`${baseURL}`, {
          method: "post",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(postData),
        });
        if (!res.ok) {
          const message = `An error has occured: ${res.status} - ${res.statusText}`;
          throw new Error(message);
        }
        const data = await res.json();
        const result = {
          data: data,
        };
        this.moves.push(data);
        console.log(data);

        let winner = this.calculateWinner(this.table.flat());
        if (winner !== null) {
          this.winner = winner;
          this.gameFinished = true;
        }
      } catch (err) {}
    },
    clearPostOutput() {},
  },
};
</script>

<template>
  <main class="pt-8 text-center">
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
    <h2 v-if="winner" class="text-6xl font-bold mb-8">
      Player <span class="text-yellow-500">{{ winner }}</span> wins
    </h2>
    <button
      @click="reset()"
      class="px-4 py-2 bg-cyan-300 rounded uppercase font-bold hover:bg-cyan-500 duration-300"
    >
      Reset
    </button>
    <div v-for="move in moves" :key="move" class="move">
      <p>
        {{ move.move }}
      </p>
    </div>
  </main>
</template>
