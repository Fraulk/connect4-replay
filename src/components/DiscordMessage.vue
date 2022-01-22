<template>
  <div class="DiscordMessage">
    <div class="author">
      <div class="authorAvatar">
        <img
          src="https://cdn.discordapp.com/avatars/873628046194778123/bb780540a15f10e3c5250a8e6c77dd1e.webp?size=128"
          alt=""
        />
      </div>
      <div class="authorText">
        <span class="authorName">FramedBot</span>
        <span class="authorBot">BOT</span>
        <span class="authorDate">{{
          Intl.DateTimeFormat("en-US").format(Date.now())
        }}</span>
      </div>
    </div>
    <div class="message">
      <img :src="air" />
      <span class="boardColumn" v-for="topRow in 7" :key="topRow">
        <Cursor v-if="topRow == cursor + 1" />
        <img v-else :src="air" />
      </span>
      <div
        class="boardRow"
        v-for="(row, i) in board.slice().reverse()"
        :key="i"
      >
        <Wall />
        <span class="boardColumn" v-for="(col, j) in row" :key="j">
          <YellowDot v-if="col == 0" />
          <RedDot v-else-if="col == 1" />
          <img v-else :src="air" />
        </span>
        <Wall />
      </div>
      <Ground v-for="grnd in 9" :key="grnd" />
      <div class="discordButtons">
        <div :class="['dButton', { playing: playing }]" @click="startReplay">
          Start
        </div>
        <div :class="['dButton']" @click="replaySpeed = 100">Faster</div>
        <div :class="['dButton']" @click="replaySpeed = 700">Slower</div>
      </div>
    </div>
  </div>
</template>

<script>
import Cursor from "../assets/svg/Cursor.vue";
import RedDot from "../assets/svg/RedDot.vue";
import YellowDot from "../assets/svg/YellowDot.vue";
import Wall from "../assets/svg/Wall.vue";
import Ground from "../assets/svg/Ground.vue";
import Air from "../assets/air.png";

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
      playerNames: ["Player A", "Player B"],
      playing: false,
      replaySpeed: 1000,
      air: Air,
    };
  },

  mounted() {
    // fetch for replay
    this.replay =
      window.location.search != ""
        ? String(window.location.search.substr(6)).split("-")
        : this.replay;
  },

  methods: {
    startReplay() {
      this.board = [
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
        [-1, -1, -1, -1, -1, -1, -1],
      ];
      this.cursor = 3;
      this.replaying();
    },
    replaying() {
      this.playing = true;
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
        if (this.turns < this.replay.length) this.replaying();
        else {
          this.playing = false;
          this.turns = 0;
          this.player = 0;
        }
      }, this.replaySpeed);
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

  components: {
    Cursor,
    RedDot,
    YellowDot,
    Wall,
    Ground,
  },
};
</script>

<style lang="scss" scoped>
.DiscordMessage {
  display: flex;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
  font-family: sans-serif;

  .message {
    width: fit-content;
    svg,
    img {
      width: 22px;
      height: 22px;
    }
    .boardColumn {
      display: inline-block;
      height: 22px;
    }
    .boardRow {
      height: 22px;
    }
  }

  .author {
    width: 20rem;
    display: flex;
    margin-bottom: -1.5rem;

    .authorAvatar {
      margin-right: 1.5rem;

      img {
        width: 40px;
        height: 40px;
        border-radius: 100%;
      }
    }

    .authorText {
      display: flex;
      gap: 0.5rem;
      height: fit-content;

      .authorName {
        color: #3498db;
        font-weight: 600;
      }
      .authorBot {
        height: max-content;
        margin-top: -0.2rem;
        padding: 0.2rem;
        border-radius: 3px;
        background-color: hsl(235, 85.6%, 64.7%);
        font-size: 0.7rem;
        font-weight: 600;
      }
      .authorDate {
        color: grey;
        font-weight: bold;
        font-size: 0.9rem;
      }
    }
  }

  .discordButtons {
    display: flex;
    gap: 0.5rem;

    .dButton {
      border-radius: 3px;
      font-size: 14px;
      font-weight: 500;
      line-height: 16px;
      padding: 10px 16px;
      background-color: #4f545c;
      cursor: pointer;

      &.playing {
        opacity: 0.5;
        cursor: not-allowed;
      }
    }
  }
}
</style>