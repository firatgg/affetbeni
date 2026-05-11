<script setup lang="ts">
import { ref, reactive } from 'vue'
import confetti from 'canvas-confetti'

const yesButtonScale = ref(1)
const showModal = ref(false)

// State for No button position
const noButtonStyles = reactive({
  position: 'static' as 'static' | 'fixed',
  top: 'auto',
  left: 'auto',
  transition: 'all 0.3s ease'
})

const yesButtonRef = ref<HTMLButtonElement | null>(null)
const noClickCount = ref(0)

const handleNoClick = (event: MouseEvent) => {
  yesButtonScale.value = yesButtonScale.value * 1.3 // 30% growth per click
  noClickCount.value++
  
  // Make button fixed so it positions relative to the viewport
  if (noButtonStyles.position !== 'fixed') {
    noButtonStyles.position = 'fixed'
  }
  
  // Get button dimensions
  const btn = event.target as HTMLButtonElement
  const btnWidth = btn.offsetWidth || 100
  const btnHeight = btn.offsetHeight || 50

  const margin = 20
  const maxX = window.innerWidth - btnWidth - margin
  const maxY = window.innerHeight - btnHeight - margin
  const safeMaxX = Math.max(0, maxX)
  const safeMaxY = Math.max(0, maxY)

  let randomX = 0
  let randomY = 0
  let overlap = true
  let attempts = 0

  // Try to find a non-overlapping position
  while (overlap && attempts < 50) {
    randomX = margin + Math.random() * (safeMaxX - margin)
    randomY = margin + Math.random() * (safeMaxY - margin)

    // Only check for overlap if click count <= 10
    if (noClickCount.value <= 10 && yesButtonRef.value) {
      const yesRect = yesButtonRef.value.getBoundingClientRect()
      
      // Calculate proposed No button rect
      const noRect = {
        left: randomX,
        top: randomY,
        right: randomX + btnWidth,
        bottom: randomY + btnHeight
      }

      // Check intersection
      const isOverlapping = !(noRect.right < yesRect.left || 
                             noRect.left > yesRect.right || 
                             noRect.bottom < yesRect.top || 
                             noRect.top > yesRect.bottom)
      
      if (!isOverlapping) {
        overlap = false
      }
    } else {
      // After 10 clicks, or if yes button ref missing, accept any position
      overlap = false
    }
    attempts++
  }

  noButtonStyles.left = `${randomX}px`
  noButtonStyles.top = `${randomY}px`
}

const handleYesClick = () => {
  showModal.value = true
  confetti({
    particleCount: 150,
    spread: 70,
    origin: { y: 0.6 },
    colors: ['#ff0000', '#ff69b4', '#ffd700']
  })
}
</script>

<template>
  <div class="container">
    <div v-if="!showModal" class="content">
      <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExYzRzcmQ3OTJtY2sxdXkwcGNwZDQ0YjN2NWZ2c2R6bXpqNnQ0eHJoZCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/I1nwVpCaB4k36/giphy.gif" alt="Lütfen" class="gif-image" />
      <h1 class="title">Beni affeder misin? ❤️</h1>
      <div class="buttons">
        <button 
          ref="yesButtonRef"
          class="yes-btn" 
          :style="{ transform: `scale(${yesButtonScale})`, transition: 'transform 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275)' }"
          @click="handleYesClick"
        >
          Evet
        </button>
        <button 
          class="no-btn" 
          :style="noButtonStyles"
          @click="handleNoClick"
        >
          Hayır
        </button>
      </div>
    </div>

    <div v-if="showModal" class="modal-overlay">
      <div class="modal-content">
        <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExd2p2Y3Q0d2N6b3R6aXJ1bXN4cXQ0aXJ6aXJ1bXN4cXQ0aXJ6aXJ1bSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/26BRv0ThflsHCqDrG/giphy.gif" alt="Mutlu" class="gif-image modal-gif" />
        <h2>Teşekkürler! Seni Seviyorum! 💖</h2>
        <p>Beni affettiğin için çok mutluyum!</p>
      </div>
    </div>
    <div class="footer">
      <a href="https://github.com/firatgg" target="_blank" rel="noopener noreferrer" style="color: inherit; text-decoration: none;">GitHub: firatgg</a>
    </div>
  </div>
</template>
