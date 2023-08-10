<script setup lang="ts">
import { ref } from 'vue'

class Point {
  x = 0
  y = 0
}

const rectRef = ref<HTMLDivElement>()
const rotationAngle = ref(0)
const origin = new Point()
let initialDistance = 0

/**
 * 计算两点间距离
 * @param p1
 * @param p2
 */
function calculateDistance(p1: Point, p2: Point) {
  return Math.sqrt((p2.x - p1.x) ** 2 + (p2.y - p1.y) ** 2)
}

function rotateMousedown(e: MouseEvent) {
  const rect = rectRef.value?.getBoundingClientRect()

  if (!rect)
    return

  origin.x = rect.left + rect.width / 2
  origin.y = rect.top + rect.height / 2

  initialDistance = calculateDistance(origin, { x: e.clientX, y: e.clientY })

  const mousemoveHandler = (e: MouseEvent) => {
    const newDistance = calculateDistance(origin, { x: e.clientX, y: e.clientY })

    // cosTheta = (a^2 + b^2 -c^2) / 2ab
    const cosTheta
      = (initialDistance ** 2 + rect.width ** 2 - newDistance ** 2)
      / (2 * initialDistance * rect.width)

    // 余弦值转为弧度再转为角度
    rotationAngle.value = Math.acos(cosTheta) * (180 / Math.PI)
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
