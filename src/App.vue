<template>
  <h1 class="logo">
    StopWatch
  </h1>
  <div :class="{ timer: true, main_object: running }">
    {{ time }}
  </div>
  <div class="buttons">
    <transition name="fade" mode="in-out">
      <button
        @click="handleClick"
        :class="{ buttons__start: !running, buttons__stop: running, animeButtons: true }"
      >
        {{ running ? 'Stop Watch' : 'Start Watch' }}
      </button>
    </transition>
    <button @click="resetWatch" :class="{ buttons__reset: true, animeButtons: true }">
      Reset Watch
    </button>
  </div>
</template>

<script lang="ts">
// eslint-disable-next-line
import { computed, defineComponent, onMounted, ref, watch } from 'vue';
import gsap from 'gsap';

export default defineComponent({
  name: 'App',
  setup() {
    const rawTime = ref(0);
    const interval = ref(0);
    const running = ref(false);

    const time = computed(() => {
      let hours: string | number = Math.floor(rawTime.value / 3600);
      let minutes: string | number = Math.floor((rawTime.value - hours * 3600) / 60);
      let seconds: string | number = rawTime.value - hours * 3600 - minutes * 60;

      if (hours < 10) {
        hours = `0${hours}`;
      }
      if (minutes < 10) {
        minutes = `0${minutes}`;
      }
      if (seconds < 10) {
        seconds = `0${seconds}`;
      }
      return `${hours}:${minutes}:${seconds}`;
    });

    onMounted(() => {
      const masterTL = gsap.timeline().pause();

      masterTL.from('.logo', {
        delay: 0.5,
        y: 'random(-200, 200)',
        ease: 'back.out',
        opacity: 0,
        scale: 0.2,
      });
      masterTL.from('.timer', {
        opacity: 0,
        y: 'random(-200, 200)',
        ease: 'back.out',
        scale: 0.2,
      });
      masterTL.from('.animeButtons', {
        opacity: 0,
        stagger: 0.2,
        y: 'random(-200, 200)',
        ease: 'back.out',
        scale: 0.2,
      });

      masterTL.play();
    });

    const startWatch = (): void => {
      if (running.value) return;
      interval.value = setInterval(() => {
        rawTime.value += 1;
      }, 1000);
      running.value = true;
    };

    const stopWatch = (): void => {
      if (!running.value) return;
      clearInterval(interval.value);
      running.value = false;
    };

    const resetWatch = (): void => {
      rawTime.value = 0;
    };

    const handleClick = () => {
      if (running.value) stopWatch();
      else startWatch();
    };

    return {
      time,
      running,
      resetWatch,
      handleClick,
    };
  },
});
</script>

<style lang="scss">
*,
*::after,
*::before {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

body {
  min-height: 100vh;
  text-align: center;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell,
    'Open Sans', 'Helvetica Neue', sans-serif;
  overflow: hidden;
}

#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-content: center;
  background-color: rgb(32, 32, 32);
  color: whitesmoke;

  .timer {
    margin: 2rem 0;
    font-size: 36px;
    transition: margin 250ms ease-out, font-size 250ms ease-out;

    &.main_object {
      margin: 4rem 0;
      font-size: 5rem;
    }
  }

  .buttons {
    display: flex;
    flex-wrap: wrap;
    width: 100%;
    justify-content: space-evenly;

    * {
      outline: none;
      border: none;
      padding: 15px 20px;
      border-radius: 5px;
      font-size: 1.5rem;
      transform: translate(-3px, -5px);
      box-shadow: 5px 7px 0 black;
      transition: transform 100ms ease-out, box-shadow 100ms ease-out;

      &:active {
        transform: translate(0, 0);
        box-shadow: none;
      }
    }

    .buttons__start {
      $bg_color: rgb(38, 182, 38);
      background-color: $bg_color;

      &:hover {
        background-color: darken($bg_color, 15);
      }
    }

    .buttons__stop {
      $bg_color: rgb(40, 40, 199);

      background-color: $bg_color;
      color: white;

      &:hover {
        background-color: darken($bg_color, 15);
      }
    }

    .buttons__reset {
      $bg_color: rgb(221, 11, 11);

      background-color: $bg_color;
      color: whitesmoke;

      &:hover {
        background-color: darken($bg_color, 15);
      }
    }
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
