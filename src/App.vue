<template>
  <div class="traffic-light-wrapper">
    <div class="rope"></div>
    <div :class="[light.color, { blink: blink }]" class="traffic-light wind">
      <div class="light red-light"></div>
      <div class="light yellow-light"></div>
      <div class="light green-light"></div>
    </div>
  </div>
</template>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

#app {
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  background: radial-gradient(134% 182% at 120% 34%, #fdfdfd 0%, #2783c4 100%);
}

.traffic-light-wrapper {
  position: relative;
  top: 15vh;
}

.rope {
  width: 100vw;
  height: 1.6vh;
  background-color: black;
}

.traffic-light {
  position: relative;
  top: -2.77vh;
  left: 15vw;

  display: flex;
  width: 26.8vh;
  height: 64.4vh;
  padding-bottom: 3.3vh;

  flex-direction: column;
  justify-content: flex-end;
  align-items: center;
  gap: 2.1vh;

  background-image: url("./assets/traffic-light.svg");
  background-repeat: no-repeat;
  background-size: contain;
}
.wind {
  animation-name: wind;
  animation-timing-function: linear;
  animation-duration: 15s;
  animation-iteration-count: infinite;
  transform-origin: 50% 0;
}
@keyframes wind {
  25% {
    transform: rotate(3deg);
  }
  60% {
    transform: rotate(0);
  }
  85% {
    transform: rotate(-2deg);
  }
}

.light {
  width: 12.7vh;
  height: 12.7vh;

  background-image: url("./assets/light-off.svg");
  background-repeat: no-repeat;
  background-size: cover;
}
.red > .red-light {
  background-image: url("./assets/red-light.svg");
}
.yellow > .yellow-light {
  background-image: url("./assets/yellow-light.svg");
}
.green > .green-light {
  background-image: url("./assets/green-light.svg");
}

.blink > .light {
  animation-name: blink;
  animation-timing-function: linear;
  animation-duration: 1s;
  animation-iteration-count: infinite;
}
@keyframes blink {
  50% {
    background-image: url("./assets/light-off.svg");
  }
}
</style>

<script>
export default {
  data() {
    return {
      colorList: [
        { color: "red", time: 10000, isBlinking: true, blinkingTime: 3000 },
        { color: "yellow", time: 3000, isBlinking: false, blinkingTime: 0 },
        { color: "green", time: 15000, isBlinking: true, blinkingTime: 3000 },
      ],
      light: { color: "" },
      next: null,
      order: 1,
      blink: false,
    };
  },

  methods: {
    changeColor() {
      this.light = this.colorList.find(
        (light) => light.color === this.$route.params.color
      );

      const currentColorIndex = this.colorList.indexOf(this.light);
      if (currentColorIndex === 0) this.order = 1;
      if (currentColorIndex === this.colorList.length - 1) this.order = -1;
      const next = this.colorList[currentColorIndex + this.order];

      if (this.light.isBlinking) {
        const timeLeftToBlinking = this.light.time - this.light.blinkingTime;
        setTimeout(() => {
          this.blink = true;
        }, timeLeftToBlinking);
      }
      setTimeout(() => {
        this.blink = false;
        this.$router.push({
          name: "traffic-light",
          params: { color: next.color },
        });
      }, this.light.time);
    },

    isEmpty(string) {
      if (string === "" || string === undefined || string === null) return true;
      return false;
    },
  },

  watch: {
    $route() {
      if (this.isEmpty(this.$route.params.color)) {
        let newColor;
        if (!this.isEmpty(localStorage.color)) newColor = localStorage.color;
        else newColor = this.colorList[0].color;

        this.$router.push({
          name: "traffic-light",
          params: { color: newColor },
        });
      } else {
        localStorage.color = this.$route.params.color;
        this.changeColor();
      }
    },
  },
};
</script>
