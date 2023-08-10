<script setup lang="ts">
import { ref } from 'vue'

class Point {
  x = 0
  y = 0
}

const rectRef = ref<HTMLDivElement>()
const rotationAngle = ref(0)
const origin = new Point()
let initialAngle = 0

/**
 * 计算鼠标位置与圆心的角度
 * @param p1
 * @param p2
 */
function calculateAngle(p1: Point, p2: Point) {
  return Math.atan2(p2.y - p1.y, p2.x - p1.x) * (180 / Math.PI)
}

function rotateMousedown(e: MouseEvent) {
  const rect = rectRef.value?.getBoundingClientRect()

  if (!rect)
    return

  origin.x = rect.left + rect.width / 2
  origin.y = rect.top + rect.height / 2

  // 计算初始角度
  initialAngle = calculateAngle(origin, { x: e.clientX, y: e.clientY })

  const mousemoveHandler = (e: MouseEvent) => {
    // 计算鼠标位置与圆心的角度
    const currentAngle = calculateAngle(origin, { x: e.clientX, y: e.clientY })
    // 计算旋转角度的差值
    rotationAngle.value = currentAngle - initialAngle
  }

  const mouseupHandler = () => {
    document.removeEventListener('mousemove', mousemoveHandler)
    document.removeEventListener('mouseup', mouseupHandler)
  }

  document.addEventListener('mousemove', mousemoveHandler)
  document.addEventListener('mouseup', mouseupHandler)
}
</script>

<template>
  <div class="relative h-screen flex flex-col items-center justify-center">
    <div
      ref="rectRef"
      class="absolute h10 w10 border-1 border-indigo border-solid bg-blue text-center line-height-10"
      :style="{ transform: `rotate(${rotationAngle}deg)` }"
    >
      <div
        i-carbon-sun
        dark:i-carbon-moon
        absolute
        class="h2 w2 -m-3"
        @mousedown="rotateMousedown"
      />
    </div>
  </div>
</template>
