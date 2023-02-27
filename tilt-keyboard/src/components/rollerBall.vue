<template>
  <canvas
    ref="canvas"
    :width="props.keyboardWidth"
    :height="props.keyboardHeight"
    class="absolute top-0 left-0 pointer-events-none"
  ></canvas>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue';
import type { Ref } from 'vue';

const props = defineProps({
  keyboardWidth: {
    type: Number,
    default: 0,
  },
  keyboardHeight: {
    type: Number,
    default: 0,
  },
});
const emit = defineEmits(['ballPosition']);

// ball properties
const x = ref(0);
const y = ref(0);
const r = ref(5);
const vx = ref(0);
const vy = ref(0);
const ax = ref(0);
const ay = ref(0);

// canvas context
let ctx: CanvasRenderingContext2D | null | undefined = null;

// get canvas reference
const canvas = ref(null) as Ref<HTMLCanvasElement | null>;

onMounted(() => {
  // get canvas context
  ctx = canvas.value?.getContext('2d');

  // set initial ball position
  if (props.keyboardHeight != 0) {
    x.value = props.keyboardWidth ;
    y.value = props.keyboardHeight;
  }
  // listen to deviceorientation event
  window.addEventListener('deviceorientation', (event) => {
    // get rotation values
    const { alpha, beta, gamma } = event;
    // convert degrees to radians
    const alphaRad = (alpha || 0 * Math.PI) / 180;
    const betaRad = ((beta || 0) * Math.PI) / 180;
    const gammaRad = (gamma || 0 * Math.PI) / 180;
    // calculate acceleration based on rotation
    ax.value = Math.sin(gammaRad);
    ay.value = Math.sin(betaRad);
  });
  // start animation loop
  animate();
});

function animate() {
  // clear canvas
  if (ctx) {
    ctx.clearRect(0, 0, props.keyboardWidth, props.keyboardHeight);
    // draw ball
    drawBall();
    // update ball position and velocity
    updateBall();
    // check for collision with walls
    checkCollision();
    // request next frame
    requestAnimationFrame(animate);
  }
}
function drawBall() {
  if (ctx) {
    // set fill color
    ctx.fillStyle = 'red';
    // begin path
    ctx.beginPath();
    // draw circle
    ctx.arc(x.value, y.value, r.value, 0, Math.PI * 2);
    // fill path
    ctx.fill();
  }
}

function updateBall() {
  // update velocity based on acceleration
  vx.value += ax.value / 10;
  vy.value += ay.value / 10;
  // apply some friction
  vx.value *= 0.99;
  vy.value *= 0.99;
  // update position based on velocity
  x.value += vx.value;
  y.value += vy.value;

  // get canvas element and its bounding rect
  const canvasEl = canvas.value;
  const canvasRect = canvasEl?.getBoundingClientRect();
  if (canvasRect) {
    // calculate absolute position of ball on page
    const absX = canvasRect.left + x.value + r.value;
    const absY = canvasRect.top + y.value + r.value;

    // emit ball position
    emit('ballPosition', { x: absX, y: absY });
  }
}

function checkCollision() {
  // check for collision with left and right walls
  if (x.value - r.value < 0 || x.value + r.value > props.keyboardWidth) {
    // reverse x velocity and apply some bounce
    vx.value = -vx.value * 0.8;
    // adjust x position to prevent overlap
    x.value = Math.max(r.value, Math.min(x.value, props.keyboardWidth - r.value));
  }
  // check for collision with top and bottom walls
  if (y.value - r.value < 0 || y.value + r.value > props.keyboardHeight) {
    // reverse y velocity and apply some bounce
    vy.value = -vy.value * 0.8;
    // adjust y position to prevent overlap
    y.value = Math.max(r.value, Math.min(y.value, props.keyboardHeight - r.value));
  }
}
</script>
