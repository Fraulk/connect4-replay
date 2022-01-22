<template>
  <div class="DiscordMessage">
    <div @click="startReplay">Start Replay</div>
    {{ board }}
  </div>
</template>

<script>
export default {
  data() {
    return {
      board: [
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
      ],
      replay: [
        "add",
        "right",
        "add",
        "left",
        "add",
        "right",
        "add",
        "left",
        "add",
        "right",
        "add",
        "left",
        "add",
      ],
      cursor: 3,
      turns: 0,
      player: 0,
    };
  },

  mounted() {
    // fetch for replay
  },

  methods: {
    startReplay() {
      setTimeout(() => {
        switch (this.replay[this.turns]) {
          case "maxLeft":
            this.cursor = 0;
            break;

          case "left":
            this.cursor -= this.cursor > 0 ? 1 : 0;
            break;

          case "add":
            this.addToken();
            this.player = this.player ? 0 : 1;
            break;

          case "right":
            this.cursor += this.cursor < 6 ? 1 : 0;
            break;

          case "maxRight":
            this.cursor = 6;
            break;

          default:
            break;
        }
        this.turns++;
        console.log(this.turns);
        if (this.turns < this.replay.length) this.startReplay();
      }, 1000);
    },

    addToken() {
      let added = false;
      this.board.map((item) => {
        if (!added && item[this.cursor] == -1) {
          item[this.cursor] = this.player;
          added = true;
        }
      });
    },
  },
};
</script>