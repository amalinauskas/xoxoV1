<script>
const baseURL = "http://localhost:5000/moves";
export default {
  name: "App",
  data() {
    return {
      postResult: null,
    };
  },
  methods: {
    fortmatResponse(res) {
      return JSON.stringify(res, null, 2);
    },
    async postData() {
      const postData = {
        move: document.getElementById("trackMovesID").innerHTML,
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
        this.postResult = this.fortmatResponse(result);
      } catch (err) {
        this.postResult = err.message;
      }
    },
    clearPostOutput() {},
  },
};
</script>
<!--  -->
<script setup>
import { ref, computed, watch, onMounted } from "vue";
import TrackMove from "./components/TrackMove.vue";

const player = ref("X");
const table = ref([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
]);

const CalculateWinner = (squares) => {
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
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
};

const winner = computed(() => CalculateWinner(table.value.flat()));

const Move = (x, y) => {
  if (winner.value) return;
  if (table.value[x][y] !== "") return;
  table.value[x][y] = player.value;
  player.value = player.value === "X" ? "O" : "X";

  // to consoleLog
  const trackMoves = table.value.flat().toString();
  document.getElementById("trackMovesID").innerHTML = trackMoves;
  console.log(trackMoves);
};

const Reset = () => {
  table.value = [
    ["", "", ""],
    ["", "", ""],
    ["", "", ""],
  ];
  player.value = "X";
};

// sessionStorage wins

const history = ref([]);
watch(winner, (current, previous) => {
  if (current && !previous) {
    history.value.push(current);
    sessionStorage.setItem("history", JSON.stringify(history.value));
  }
});

onMounted(() => {
  history.value = JSON.parse(sessionStorage.getItem("history")) ?? [];
});
</script>

<template>
  <main class="pt-8 text-center">
    <h3 class="text-xl mb-4">Player {{ player }} turn</h3>

    <div class="flex flex-col items-center mb-8">
      <div v-for="(row, x) in table" :key="x" class="flex">
        <div
          v-for="(cell, y) in row"
          :key="y"
          @click="Move(x, y)"
          :class="`border border-black w-24 h-24 hover:bg-gray-200 duration-300 flex items-center justify-center material-icons-outlined text-4xl cursor-pointer ${
            cell === 'X' ? 'text-lime-400' : 'text-cyan-400'
          }`"
        >
          {{ cell === "X" ? "X" : cell === "O" ? "O" : "" }}
        </div>
      </div>
    </div>
    <h2 v-if="winner" class="text-6xl font-bold mb-8">
      Player '<span class="text-yellow-500">{{ winner }}</span
      >' wins
    </h2>
    <button
      @click="Reset"
      class="px-4 py-2 bg-cyan-300 rounded uppercase font-bold hover:bg-cyan-500 duration-300"
    >
      Reset
    </button>

    <!-- sesions -->

    <h2 class="mt-5 text-4xl">History</h2>
    <div v-for="(game, idx) in history" :key="idx">
      Game{{ idx + 1 }}: {{ game }} won
    </div>

    <!-- moves  -->

    <div>
      <p class="mt-5 text-4xl">Moves made</p>
      <p class="mt-5 text-1xl" id="trackMovesID"></p>
      <!-- Post data to BE -->
      <div id="app" class="content-start">
        <div>
          <div class="mt-5 text-4xl">Vue Fetch POST moves</div>
          <div class="rounded-sm">
            <button
              class="mt-5 px-4 py-2 bg-cyan-300 rounded uppercase font-bold hover:bg-cyan-500 duration-300"
              @click="postData"
            >
              Post Data
            </button>
            <TrackMove />
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<style></style>
