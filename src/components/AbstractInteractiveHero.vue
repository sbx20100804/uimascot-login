<template>
  <div class="relative flex flex-col items-center justify-center gap-10 py-8">
    <div ref="circleRef" class="character">
      <div class="face circle-face">
        <div class="eyes">
          <div class="eye">
            <div ref="circleLeftPupil" class="pupil"></div>
          </div>
          <div class="eye">
            <div ref="circleRightPupil" class="pupil"></div>
          </div>
        </div>
        <div ref="circleLid" class="lid circle-lid"></div>
      </div>
    </div>

    <div ref="squareRef" class="character">
      <div ref="squareFace" class="face square-face">
        <div class="eyes">
          <div class="eye">
            <div ref="squareLeftPupil" class="pupil"></div>
          </div>
          <div class="eye">
            <div ref="squareRightPupil" class="pupil"></div>
          </div>
        </div>
        <div ref="squareLid" class="lid square-lid"></div>
      </div>
    </div>

    <div ref="capsuleRef" class="character">
      <div class="face capsule-face">
        <div class="eyes">
          <div class="eye">
            <div ref="capsuleLeftPupil" class="pupil"></div>
          </div>
          <div class="eye">
            <div ref="capsuleRightPupil" class="pupil"></div>
          </div>
        </div>
        <div ref="capsuleLid" class="lid capsule-lid"></div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch, nextTick } from 'vue'
import gsap from 'gsap'

const props = defineProps({
  focusState: { type: String, default: 'none' },
  loginStatus: { type: String, default: 'idle' },
  isButtonHovered: { type: Boolean, default: false },
})

const circleRef = ref(null)
const squareRef = ref(null)
const capsuleRef = ref(null)

const circleLeftPupil = ref(null)
const circleRightPupil = ref(null)
const squareLeftPupil = ref(null)
const squareRightPupil = ref(null)
const capsuleLeftPupil = ref(null)
const capsuleRightPupil = ref(null)

const circleLid = ref(null)
const squareLid = ref(null)
const capsuleLid = ref(null)

let currentState = 'idle'
let blinkTimer = null
let mainTimeline = null

function killAllAnimations() {
  if (mainTimeline) {
    mainTimeline.kill()
    mainTimeline = null
  }
  gsap.killTweensOf([
    circleRef.value, squareRef.value, capsuleRef.value,
    circleLeftPupil.value, circleRightPupil.value,
    squareLeftPupil.value, squareRightPupil.value,
    capsuleLeftPupil.value, capsuleRightPupil.value,
    circleLid.value, squareLid.value, capsuleLid.value
  ])
}

function handleMouseMove(e) {
  if (currentState === 'password') return

  const cx = window.innerWidth / 2
  const cy = window.innerHeight / 2
  const dx = (e.clientX - cx) / cx
  const dy = (e.clientY - cy) / cy

  const cX = dx * 8
  const cY = dy * 8
  const sX = dx * 6
  const sY = dy * 6
  const capX = dx * 4
  const capY = dy * 4

  gsap.to([circleLeftPupil.value, circleRightPupil.value], { x: cX, y: cY, duration: 0.15, ease: 'power1.out' })
  gsap.to([squareLeftPupil.value, squareRightPupil.value], { x: sX, y: sY, duration: 0.15, ease: 'power1.out' })
  gsap.to([capsuleLeftPupil.value, capsuleRightPupil.value], { x: capX, y: capY, duration: 0.15, ease: 'power1.out' })
}

function blink() {
  if (currentState === 'password') return

  const tl = gsap.timeline()
  tl.to([circleLid.value, squareLid.value, capsuleLid.value], { y: 0, duration: 0.08, ease: 'power2.in' })
    .to(circleLid.value, { y: -60, duration: 0.1, ease: 'power2.out', delay: 0.06 })
    .to(squareLid.value, { y: -55, duration: 0.1, ease: 'power2.out' }, '-=0.1')
    .to(capsuleLid.value, { y: -45, duration: 0.1, ease: 'power2.out' }, '-=0.1')
}

function scheduleBlink() {
  if (blinkTimer) clearTimeout(blinkTimer)
  
  const delay = Math.random() * 3000 + 2000
  blinkTimer = setTimeout(() => {
    if (currentState !== 'password') blink()
    scheduleBlink()
  }, delay)
}

function playStart() {
  killAllAnimations()
  
  gsap.set([circleRef.value, squareRef.value, capsuleRef.value], { y: 100, opacity: 0, scale: 0.5 })
  gsap.set([circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value, capsuleLeftPupil.value, capsuleRightPupil.value], { scale: 0 })
  gsap.set(circleLid.value, { y: -60 })
  gsap.set(squareLid.value, { y: -55 })
  gsap.set(capsuleLid.value, { y: -45 })

  mainTimeline = gsap.timeline()
  mainTimeline.to([circleRef.value, squareRef.value, capsuleRef.value], { opacity: 1, duration: 0.6, ease: 'power2.out' })
    .to([circleRef.value, squareRef.value, capsuleRef.value], { y: 0, scale: 1, duration: 1, stagger: 0.15, ease: 'back.out(1.5)' }, '-=0.3')
    .to([circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value, capsuleLeftPupil.value, capsuleRightPupil.value], { scale: 1, duration: 0.3, ease: 'back.out(2.5)', stagger: 0.08 }, '-=0.4')
}

function startIdle() {
  gsap.to(circleRef.value, { y: -6, duration: 3, ease: 'sine.inOut', yoyo: true, repeat: -1 })
  gsap.to(squareRef.value, { y: -5, duration: 2.8, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 0.3 })
  gsap.to(capsuleRef.value, { y: -4, duration: 2.5, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 0.6 })
}

function toEmail() {
  killAllAnimations()
  currentState = 'email'
  
  mainTimeline = gsap.timeline()
  mainTimeline.to([circleRef.value, squareRef.value, capsuleRef.value], { x: 12, rotation: 1.5, y: 0, duration: 0.35, ease: 'power2.out' })
    .to([circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value], { x: 1.5, y: 0, duration: 0.3, ease: 'power2.out' }, '-=0.2')
}

function toPassword() {
  killAllAnimations()
  currentState = 'password'
  
  mainTimeline = gsap.timeline()
  mainTimeline.to([circleRef.value, squareRef.value, capsuleRef.value], { x: 0, rotation: 0, y: 0, duration: 0.3, ease: 'power2.out' })
    .to(circleLid.value, { y: 0, duration: 0.2 }, '-=0.1')
    .to(squareLid.value, { y: 0, duration: 0.2 }, '-=0.15')
    .to(capsuleLid.value, { y: 0, duration: 0.2 }, '-=0.15')
}

function toIdle() {
  killAllAnimations()
  currentState = 'idle'
  
  mainTimeline = gsap.timeline()
  mainTimeline.to([circleRef.value, squareRef.value, capsuleRef.value], { x: 0, rotation: 0, y: 0, duration: 0.35, ease: 'power2.out' })
    .to(circleLid.value, { y: -60, duration: 0.3, ease: 'back.out(1.5)' }, '-=0.1')
    .to([circleLeftPupil.value, circleRightPupil.value], { y: 0, x: 0, duration: 0.35 }, '-=0.2')
    .to(squareLid.value, { y: -55, duration: 0.3, ease: 'back.out(1.5)' }, '-=0.25')
    .to([squareLeftPupil.value, squareRightPupil.value], { y: 0, x: 0, duration: 0.35 }, '-=0.2')
    .to(capsuleLid.value, { y: -45, duration: 0.3, ease: 'back.out(1.5)' }, '-=0.25')
  
  setTimeout(() => {
    if (currentState === 'idle') {
      startIdle()
    }
  }, 400)
}

function onHover() {
  if (currentState === 'password') return
  
  const yOff = currentState === 'email' ? -12 : -8
  gsap.to([circleRef.value, squareRef.value, capsuleRef.value], { y: yOff, duration: 0.35, ease: 'power2.out' })
}

function onLeave() {
  if (currentState === 'password') return
  
  const yOff = currentState === 'email' ? -6 : 0
  gsap.to([circleRef.value, squareRef.value, capsuleRef.value], { y: yOff, duration: 0.3, ease: 'power2.out' })
}

function shake() {
  killAllAnimations()
  
  gsap.to([circleRef.value, squareRef.value, capsuleRef.value], {
    keyframes: [
      { x: -12, duration: 0.07, rotation: -6 },
      { x: 12, duration: 0.07, rotation: 6 },
      { x: -8, duration: 0.05, rotation: -4 },
      { x: 8, duration: 0.05, rotation: 4 },
      { x: 0, duration: 0.06, rotation: 0 },
    ],
    ease: 'power2.inOut',
  })
}

watch(() => props.focusState, async (newS, oldS) => {
  await nextTick()
  
  if (props.loginStatus === 'error') return
  
  if (newS === 'email') {
    toEmail()
  } else if (newS === 'password') {
    toPassword()
  } else if (newS === 'none' && oldS !== 'none') {
    toIdle()
  }
})

watch(() => props.loginStatus, async (s) => {
  await nextTick()
  if (s === 'error') {
    shake()
  }
})

watch(() => props.isButtonHovered, async (h) => {
  await nextTick()
  h ? onHover() : onLeave()
})

onMounted(() => {
  document.addEventListener('mousemove', handleMouseMove)
  playStart()
  setTimeout(() => {
    if (currentState === 'idle') {
      startIdle()
      scheduleBlink()
    }
  }, 1200)
})

onUnmounted(() => {
  document.removeEventListener('mousemove', handleMouseMove)
  if (blinkTimer) clearTimeout(blinkTimer)
  killAllAnimations()
})
</script>

<style scoped>
.character {
  perspective: 1000px;
}

.face {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.circle-face {
  width: 11rem;
  height: 11rem;
  background: #E2E8F0;
  border-radius: 50%;
  box-shadow: 14px 14px 28px rgba(148, 163, 184, 0.28), -14px -14px 28px rgba(255, 255, 255, 0.85), inset 4px 4px 9px rgba(148, 163, 184, 0.18), inset -4px -4px 9px rgba(255, 255, 255, 0.65);
}

.square-face {
  width: 9.5rem;
  height: 9.5rem;
  background: #FEE2E2;
  border-radius: 1.4rem;
  backface-visibility: hidden;
  box-shadow: 12px 12px 24px rgba(248, 113, 113, 0.23), -12px -12px 24px rgba(255, 255, 255, 0.82), inset 3.5px 3.5px 7px rgba(248, 113, 113, 0.14), inset -3.5px -3.5px 7px rgba(255, 255, 255, 0.58);
}

.capsule-face {
  width: 12rem;
  height: 6.5rem;
  background: #DBEAFE;
  border-radius: 9999px;
  box-shadow: 8px 8px 16px rgba(96, 165, 250, 0.16), -8px -8px 16px rgba(255, 255, 255, 0.82), inset 2.5px 2.5px 5px rgba(96, 165, 250, 0.11), inset -2.5px -2.5px 5px rgba(255, 255, 255, 0.52);
}

.eyes {
  display: flex;
  gap: 38px;
  position: relative;
  z-index: 10;
}

.eye {
  width: 26px;
  height: 26px;
  position: relative;
  overflow: hidden;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.82);
  box-shadow: inset 2.5px 2.5px 5px rgba(0, 0, 0, 0.09);
}

.pupil {
  width: 17px;
  height: 17px;
  background: #1E293B;
  border-radius: 50%;
  position: absolute;
  top: 4.5px;
  left: 4.5px;
}

.lid {
  position: absolute;
  overflow: hidden;
  z-index: 20;
}

.circle-lid {
  width: 11rem;
  height: 6rem;
  top: 0;
  left: 0;
  background: #E2E8F0;
  border-radius: 11rem 11rem 0 0;
  box-shadow: 0 4px 8px rgba(148, 163, 184, 0.18);
}

.square-lid {
  width: 9.5rem;
  height: 5.2rem;
  top: 0;
  left: 0;
  background: #FEE2E2;
  border-radius: 1.4rem 1.4rem 0 0;
  box-shadow: 0 3.5px 7px rgba(248, 113, 113, 0.14);
}

.capsule-lid {
  width: 12rem;
  height: 4rem;
  top: 0;
  left: 0;
  background: #DBEAFE;
  border-radius: 9999px 9999px 0 0;
  box-shadow: 0 3px 6px rgba(96, 165, 250, 0.11);
}
</style>
