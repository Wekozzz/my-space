<template>
  <div
    style="width: 100%"
    class="flex justify-center items-center absolute bottom-0 z-200"
  >
    <div class="container" ref="containerRef">
      <div class="blackBox">
        <div class="blackBoxPoint" />
      </div>
      <div class="whiteBox" @click="skip('/note')">
        <div class="whiteBoxPoint" />
        <div class="name text-1rem mb-14 text-center min-h-36 h-36">
          {{ $t('note') }}
          <img src="/svg/skip.svg" class="w-15" />
        </div>
        <div class="value text-1rem font-500">$656</div>
      </div>
      <div class="blackBox">
        <div class="blackBoxPoint" />
      </div>
      <div class="whiteBox" @click="skip('/blog')">
        <div class="whiteBoxPoint" />
        <div class="name text-1rem mb-14 text-center min-h-36 h-36">
          {{ $t('blog') }}
          <img src="/svg/skip.svg" class="w-15" />
        </div>
        <div class="value text-1rem font-500">84351</div>
      </div>
      <div class="blackBox">
        <div class="blackBoxPoint" />
      </div>
      <div class="whiteBox" @click="skip('/curriculum')">
        <div class="whiteBoxPoint" />
        <div class="name text-1rem mb-14 text-center min-h-36 h-36">
          {{ $t('curriculum') }}
          <img src="/svg/skip.svg" class="w-15" />
        </div>
        <div class="value text-1rem font-500">64</div>
      </div>
      <div class="blackBox">
        <div class="blackBoxPoint" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';

import { useRouter } from 'vue-router';

type SkipType = '/note' | '/blog' | '/curriculum';

const router = useRouter();
const skip = (goSkip: SkipType) => {
  router.push(goSkip);
};

const pointWidth = 8;
const boxRadius = 8;
let frameId = 0;
let movePointIndex = 0;
let movePointV = 4;

const containerRef = ref<HTMLDivElement>();

// Move the light point
const movePoint = () => {
  if (containerRef.value) {
    const nativeNode = containerRef.value as HTMLDivElement;
    if (nativeNode) {
      const { length } = nativeNode.children;
      const box = nativeNode.children[movePointIndex] as HTMLDivElement;
      const point = box?.children[0] as HTMLDivElement;
      const { clientWidth, clientHeight } = box;
      const { style } = point;
      if (movePointIndex % 2 === 0) {
        let left = boxRadius;
        if (style.left) {
          left = parseFloat(style.left);
        }
        let newLeft = left + movePointV;
        if (newLeft >= clientWidth - boxRadius - pointWidth) {
          point.style.visibility = 'hidden';
          if (movePointIndex >= length - 1) {
            movePointIndex = 0;
            const nextPoint = (
              nativeNode.children[movePointIndex] as HTMLDivElement
            )?.firstChild as HTMLDivElement;
            nextPoint.style.left = `${boxRadius}px`;
            nextPoint.style.visibility = 'visible';
          } else {
            movePointIndex += 1;
            const nextPoint = (
              nativeNode.children[movePointIndex] as HTMLDivElement
            )?.firstChild as HTMLDivElement;
            nextPoint.style.left = '-1px';
            nextPoint.style.visibility = 'visible';
          }
        } else {
          point.style.left = `${newLeft}px`;
          if (point.style.visibility !== 'visible') {
            point.style.visibility = 'visible';
          }
        }
      } else {
        let left = -1;
        let bottom = boxRadius;
        if (style.left) {
          left = parseFloat(style.left);
        }
        if (style.bottom) {
          bottom = parseFloat(style.bottom);
        }
        if (left >= clientWidth) {
          let newBottom = bottom - movePointV;
          if (newBottom <= boxRadius) {
            point.style.visibility = 'hidden';
            movePointIndex += 1;
            const nextPoint = (
              nativeNode.children[movePointIndex] as HTMLDivElement
            )?.firstChild as HTMLDivElement;
            nextPoint.style.left = `${boxRadius}px`;
            nextPoint.style.visibility = 'visible';
          } else {
            point.style.bottom = `${newBottom}px`;
          }
        } else if (left <= -1) {
          let newBottom = bottom + movePointV;
          if (newBottom > clientHeight - boxRadius - pointWidth) {
            newBottom = clientHeight;
            point.style.width = `${pointWidth}px`;
            point.style.height = '1px';
            point.style.left = `${boxRadius}px`;
          }
          point.style.bottom = `${newBottom}px`;
        } else {
          let newLeft = left + movePointV;
          if (newLeft > clientWidth - boxRadius - pointWidth) {
            newLeft = clientWidth;
            point.style.width = '1px';
            point.style.height = `${pointWidth}px`;
            point.style.bottom = `${clientHeight - boxRadius - pointWidth}`;
          }
          point.style.left = `${newLeft}px`;
        }
      }

      frameId = requestAnimationFrame(movePoint);
    }
  }
};

onMounted(() => {
  if (containerRef.value) {
    frameId = requestAnimationFrame(movePoint);
  }
});

onUnmounted(() => {
  frameId && cancelAnimationFrame(frameId);
});
</script>

<style scoped lang="scss">
.container {
  box-sizing: border-box;
  width: 100%;
  height: 240px;
  display: flex;
  align-items: center;
  border-radius: 10px;
  overflow: hidden;

  $boxHeight: 200px;
  $blackBoxWidth: 42px;
  $pointWidth: 8px;
  $radius: 8px;

  .blackBox {
    background-color: transparent;
    border-bottom: 1px solid #bb3dfe50;
    border-bottom-left-radius: $radius;
    border-bottom-right-radius: $radius;
    width: $blackBoxWidth;
    height: 114px;
    margin: 0 -1px (-$radius);
    transform: translateY($boxHeight);
    animation: entrance_totop 0.6s 1 forwards 0s alternate;
    position: relative;

    .blackBoxPoint {
      position: absolute;
      left: $radius;
      bottom: -1px;
      width: $pointWidth;
      height: 1px;
      background-color: #ffffff;
      box-shadow: 0 0 10px 3px #ffffff;
      visibility: hidden;
    }

    &:first-child {
      display: flex;
      flex: 1;
      border-bottom-left-radius: 0;

      .blackBoxPoint {
        visibility: hidden;
      }
    }

    &:last-child {
      display: flex;
      flex: 1;
      border-bottom-right-radius: 0;
    }
  }

  .whiteBox {
    background: linear-gradient(
      to bottom,
      #3f9afd30 10%,
      #bb3dfe10 70%,
      transparent
    );
    width: 200px;
    height: 120px;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: #ffffff;
    border-top: 1px solid #3f9afd;
    border-left: 1px solid transparent;
    border-right: 1px solid transparent;
    border-image: linear-gradient(#3f9afd, #bb3dfe50) 2;
    clip-path: inset(0 round $radius);
    position: relative;
    pointer-events: all;
    transform: translateY($boxHeight);
    animation: entrance_totop 0.6s 1 forwards 0s alternate;

    .whiteBoxPoint {
      position: absolute;
      left: -1px;
      bottom: $radius;
      width: 1px;
      height: $pointWidth;
      background-color: #ffffff;
      box-shadow: 0 0 10px 5px #ffffff;
      visibility: hidden;
    }

    &::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: $radius;
      height: $radius;
      border: 1px solid #3f9afd;
      border-top-left-radius: $radius;
      border-right: 1px solid transparent;
      border-bottom: 1px solid transparent;
      margin: -1px;
    }

    &::after {
      content: '';
      position: absolute;
      top: 0;
      right: 0;
      width: $radius;
      height: $radius;
      border: 1px solid #3f9afd;
      border-top-right-radius: $radius;
      border-left: 1px solid transparent;
      border-bottom: 1px solid transparent;
      margin: -1px;
    }

    .name {
      transform: translateY(100px);
      animation: entrance_totop 1.6s 1 forwards 0s alternate;
    }

    .value {
      transform: translateY($boxHeight);
      animation: entrance_totop 1.6s 1 forwards 0.3s alternate;
    }
  }

  @keyframes entrance_totop {
    0% {
      transform: translateY($boxHeight);
      opacity: 0;
    }

    100% {
      transform: translateY(0);
      opacity: 1;
    }
  }
}
</style>
