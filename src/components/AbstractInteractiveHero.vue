<template>
  <div ref="containerRef" class="relative flex flex-col items-center justify-center gap-4 sm:gap-6 md:gap-10 py-4 sm:py-6 md:py-8">
    <div class="absolute -top-4 -left-4 sm:-top-8 sm:-left-8 w-24 h-24 sm:w-32 sm:h-32 bg-gradient-to-br from-brand-200/60 to-purple-200/40 rounded-full blur-3xl animate-pulse-soft pointer-events-none"></div>
    <div class="absolute -bottom-4 -right-4 sm:-bottom-10 sm:-right-10 w-28 h-28 sm:w-36 sm:h-36 bg-gradient-to-br from-blue-200/50 to-brand-200/50 rounded-full blur-3xl animate-pulse-soft pointer-events-none" style="animation-delay: 2s"></div>
    <div class="absolute top-1/3 right-2 sm:right-4 w-14 h-14 sm:w-20 sm:h-20 bg-gradient-to-br from-cyan-200/30 to-brand-200/30 rounded-full blur-2xl animate-float-medium pointer-events-none" style="animation-delay: 1s"></div>

    <div ref="circleRef" class="character animate-float-slow cursor-pointer" @click="handleCharacterClick('circle')">
      <div class="face circle-face relative">
        <div class="absolute inset-0 rounded-full bg-gradient-to-br from-white/50 to-transparent pointer-events-none"></div>
        <div class="eyes">
          <div class="eye">
            <div ref="circleLeftPupil" class="pupil"></div>
          </div>
          <div class="eye">
            <div ref="circleRightPupil" class="pupil"></div>
          </div>
        </div>
        <div ref="circleMouth" class="mouth circle-mouth"></div>
        <div ref="circleLid" class="lid circle-lid"></div>
      </div>
    </div>

    <div ref="squareRef" class="character animate-float-medium cursor-pointer" style="animation-delay: 0.4s" @click="handleCharacterClick('square')">
      <div class="face square-face relative">
        <div class="absolute inset-0 rounded-2xl bg-gradient-to-br from-white/40 to-transparent pointer-events-none"></div>
        <div class="eyes">
          <div class="eye">
            <div ref="squareLeftPupil" class="pupil"></div>
          </div>
          <div class="eye">
            <div ref="squareRightPupil" class="pupil"></div>
          </div>
        </div>
        <div ref="squareMouth" class="mouth square-mouth"></div>
        <div ref="squareLid" class="lid square-lid"></div>
      </div>
    </div>

    <div ref="capsuleRef" class="character animate-float-fast cursor-pointer" style="animation-delay: 0.8s" @click="handleCharacterClick('capsule')">
      <div class="face capsule-face relative">
        <div class="absolute inset-0 rounded-full bg-gradient-to-br from-white/40 to-transparent pointer-events-none"></div>
        <div class="eyes">
          <div class="eye">
            <div ref="capsuleLeftPupil" class="pupil"></div>
          </div>
          <div class="eye">
            <div ref="capsuleRightPupil" class="pupil"></div>
          </div>
        </div>
        <div ref="capsuleMouth" class="mouth capsule-mouth"></div>
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

const containerRef = ref(null)
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

const circleMouth = ref(null)
const squareMouth = ref(null)
const capsuleMouth = ref(null)

let currentState = 'idle'
let blinkTimer = null
let idleTweens = []
let startTimeline = null

const chars = () => [circleRef.value, squareRef.value, capsuleRef.value].filter(Boolean)
const pupils = () => [circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value, capsuleLeftPupil.value, capsuleRightPupil.value].filter(Boolean)
const lids = () => [circleLid.value, squareLid.value, capsuleLid.value].filter(Boolean)
const mouths = () => [circleMouth.value, squareMouth.value, capsuleMouth.value].filter(Boolean)

const LID_OPEN_Y = -60
const LID_CLOSED_Y = 0

function killIdleAndStateAnimations() {
  idleTweens.forEach(t => t.kill())
  idleTweens = []
  if (startTimeline) {
    startTimeline.kill()
    startTimeline = null
  }
  gsap.killTweensOf(chars())
  gsap.killTweensOf(lids())
  gsap.killTweensOf(mouths())
  gsap.killTweensOf(pupils())
}

function startIdle() {
  idleTweens.forEach(t => t.kill())
  idleTweens = []

  idleTweens.push(
    gsap.to(circleRef.value, { y: -6, duration: 3, ease: 'sine.inOut', yoyo: true, repeat: -1 }),
    gsap.to(circleRef.value, { rotation: 2, duration: 4, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 0.8 }),
    gsap.to(squareRef.value, { y: -5, duration: 2.8, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 0.4 }),
    gsap.to(squareRef.value, { rotation: -1.5, duration: 3.5, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 1 }),
    gsap.to(capsuleRef.value, { y: -4, duration: 2.5, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 0.8 }),
    gsap.to(capsuleRef.value, { rotation: 1, duration: 3.2, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 1.2 }),
  )
}

function getScaleFactor() {
  const w = window.innerWidth
  if (w < 640) return 0.5
  if (w < 768) return 0.7
  return 1
}

function handleCharacterClick(type) {
  if (currentState === 'password') return
  const s = getScaleFactor()
  
  let charRef
  let otherChars = []
  
  if (type === 'circle') {
    charRef = circleRef
    otherChars = [squareRef, capsuleRef]
  } else if (type === 'square') {
    charRef = squareRef
    otherChars = [circleRef, capsuleRef]
  } else {
    charRef = capsuleRef
    otherChars = [circleRef, squareRef]
  }
  
  const tl = gsap.timeline()
  
  tl.to(charRef.value, { 
    scale: 1.12, 
    y: -8 * s, 
    x: (type === 'circle' ? -10 : (type === 'square' ? 0 : 10)) * s,
    duration: 0.3, 
    ease: 'back.out(1.4)' 
  }, 0)
  
  tl.to(otherChars.filter(c => c.value), { 
    x: (type === 'circle' ? 8 : (type === 'square' ? (otherChars[0] === circleRef ? -8 : 8) : -8)) * s,
    duration: 0.3, 
    ease: 'power2.out' 
  }, 0)
  
  tl.to(charRef.value, { 
    scale: 1, 
    y: 0, 
    x: 0,
    duration: 0.4, 
    ease: 'elastic.out(1, 0.5)' 
  }, 0.25)
  
  tl.to(otherChars.filter(c => c.value), { 
    x: 0,
    duration: 0.4, 
    ease: 'elastic.out(1, 0.5)' 
  }, 0.28)
}

function handleMouseMove(e) {
  if (currentState === 'password') return
  if (!containerRef.value) return
  const s = getScaleFactor()

  const rect = containerRef.value.getBoundingClientRect()
  const cx = rect.left + rect.width / 2
  const cy = rect.top + rect.height / 2
  const dx = (e.clientX - cx) / (window.innerWidth / 2)
  const dy = (e.clientY - cy) / (window.innerHeight / 2)

  const maxOffset = currentState === 'email' ? 2.5 : 5
  const cX = dx * maxOffset * s
  const cY = dy * maxOffset * s
  const sX = dx * (maxOffset - 0.8) * s
  const sY = dy * (maxOffset - 0.8) * s
  const capX = dx * (maxOffset - 1.5) * s
  const capY = dy * (maxOffset - 1.5) * s

  gsap.to([circleLeftPupil.value, circleRightPupil.value], { x: cX, y: cY, duration: 0.3, ease: 'power2.out', overwrite: true })
  gsap.to([squareLeftPupil.value, squareRightPupil.value], { x: sX, y: sY, duration: 0.3, ease: 'power2.out', overwrite: true })
  gsap.to([capsuleLeftPupil.value, capsuleRightPupil.value], { x: capX, y: capY, duration: 0.3, ease: 'power2.out', overwrite: true })

  const maxTilt = 5
  gsap.to(circleRef.value, { 
    rotation: dx * maxTilt, 
    duration: 0.35, 
    ease: 'power2.out', 
    overwrite: true 
  })
  gsap.to(squareRef.value, { 
    rotation: dx * (maxTilt * 0.8), 
    duration: 0.4, 
    ease: 'power2.out', 
    overwrite: true 
  })
  gsap.to(capsuleRef.value, { 
    rotation: dx * (maxTilt * 0.6), 
    duration: 0.45, 
    ease: 'power2.out', 
    overwrite: true 
  })

  const maxTranslate = 4 * s
  gsap.to(circleRef.value, { 
    x: dx * maxTranslate, 
    y: dy * maxTranslate, 
    duration: 0.4, 
    ease: 'power2.out', 
    overwrite: true 
  })
  gsap.to(squareRef.value, { 
    x: dx * (maxTranslate * 0.8), 
    y: dy * (maxTranslate * 0.8), 
    duration: 0.45, 
    ease: 'power2.out', 
    overwrite: true 
  })
  gsap.to(capsuleRef.value, { 
    x: dx * (maxTranslate * 0.6), 
    y: dy * (maxTranslate * 0.6), 
    duration: 0.5, 
    ease: 'power2.out', 
    overwrite: true 
  })
}

function blink() {
  if (currentState === 'password') return
  const tl = gsap.timeline()
  tl.to(lids(), { y: LID_CLOSED_Y, duration: 0.1, ease: 'power2.in' })
  tl.to(lids(), { y: LID_OPEN_Y, duration: 0.15, ease: 'power2.out', delay: 0.08 })
}

function scheduleBlink() {
  if (blinkTimer) clearTimeout(blinkTimer)
  blinkTimer = setTimeout(() => {
    if (currentState !== 'password') blink()
    scheduleBlink()
  }, Math.random() * 4000 + 2000)
}

function playStart() {
  killIdleAndStateAnimations()

  gsap.set(chars(), { y: 0, opacity: 1, scale: 1, rotation: 0 })
  gsap.set(pupils(), { scale: 1, x: 0, y: 0 })
  gsap.set(mouths(), { scaleX: 0.65, scaleY: 0.55 })
  gsap.set(lids(), { y: LID_OPEN_Y })

  startTimeline = gsap.timeline({
    onComplete: () => {
      startTimeline = null
      if (currentState === 'idle') {
        startIdle()
        scheduleBlink()
      }
    }
  })

  startTimeline
    .to(chars(), { y: -5, duration: 0.3, ease: 'power2.out', stagger: 0.08 })
    .to(chars(), { y: 0, duration: 0.3, ease: 'elastic.out(1, 0.6)', stagger: 0.08 })
}

function toEmail() {
  console.log('toEmail called!')
  killIdleAndStateAnimations()
  currentState = 'email'
  const s = getScaleFactor()

  gsap.set(chars(), { rotation: 0, x: 0, y: 0, scale: 1, opacity: 1 })
  
  const tl = gsap.timeline()
  tl.to(chars(), { x: 50 * s, rotation: 8, y: -10 * s, duration: 0.5, ease: 'power2.out', stagger: 0.08 })
  tl.to(circleRef.value, { scale: 1.15, duration: 0.4, ease: 'back.out(1.5)' }, '-=0.4')
  tl.to(lids(), { y: LID_OPEN_Y, duration: 0.3, ease: 'back.out(1.4)' }, '-=0.2')
  tl.to(mouths(), { scaleX: 1.1, scaleY: 1, duration: 0.35, ease: 'power2.out' }, '-=0.2')
  tl.to(pupils(), { x: 8 * s, duration: 0.4, ease: 'power2.out' }, '-=0.2')
}

function toPassword() {
  killIdleAndStateAnimations()
  currentState = 'password'

  gsap.set(chars(), { rotation: 0, x: 0, y: 0, scale: 1, opacity: 1 })
  gsap.set(pupils(), { x: 0, y: 0 })

  const tl = gsap.timeline()
  tl.to(chars(), { x: 0, rotation: 0, y: 0, duration: 0.35, ease: 'power2.out' })
  tl.to(chars(), { scale: 0.96, duration: 0.3, ease: 'back.in(1.1)' }, '-=0.1')
  tl.to(lids(), { y: LID_CLOSED_Y, duration: 0.25, ease: 'power2.in' }, '-=0.1')
  tl.to(mouths(), { scaleX: 0.18, scaleY: 0.12, duration: 0.3, ease: 'power2.out' }, '-=0.15')
}

function toIdle() {
  killIdleAndStateAnimations()
  currentState = 'idle'

  gsap.set(chars(), { rotation: 0, x: 0, y: 0, scale: 1, opacity: 1 })
  
  const tl = gsap.timeline()
  tl.to(lids(), { y: LID_OPEN_Y, duration: 0.35, ease: 'back.out(1.4)' })
  tl.to(mouths(), { scaleX: 0.65, scaleY: 0.55, duration: 0.4, ease: 'elastic.out(1, 0.6)' }, '-=0.2')
  tl.to(pupils(), { x: 0, y: 0, duration: 0.45, ease: 'power2.out' }, 0)
  tl.add(() => {
    if (currentState === 'idle') startIdle()
  })
}

function onHover() {
  if (currentState === 'password') return
  const s = getScaleFactor()
  idleTweens.forEach(t => t.kill())
  idleTweens = []

  const tl = gsap.timeline()
  tl.to(chars(), { y: -8 * s, duration: 0.35, ease: 'power2.out', stagger: 0.06 })
  tl.to(chars(), { scale: 1.05, duration: 0.35, ease: 'back.out(1.3)' }, '-=0.25')
  tl.to(mouths(), { scaleX: 0.75, scaleY: 0.65, duration: 0.3, ease: 'back.out(1.2)' }, '-=0.2')
}

function onLeave() {
  if (currentState === 'password') return

  const tl = gsap.timeline()
  tl.to(chars(), { y: 0, scale: 1, duration: 0.4, ease: 'elastic.out(1, 0.6)', stagger: 0.04 })
  tl.to(mouths(), { scaleX: 0.65, scaleY: 0.55, duration: 0.35, ease: 'back.out(1.1)' }, '-=0.25')
  tl.add(() => {
    if (currentState === 'idle') startIdle()
  })
}

function shake() {
  killIdleAndStateAnimations()
  currentState = 'error'
  const s = getScaleFactor()

  const tl = gsap.timeline()

  tl.to(chars(), {
    keyframes: [
      { x: -15 * s, duration: 0.12, rotation: -8 },
      { x: 15 * s, duration: 0.12, rotation: 8 },
      { x: -10 * s, duration: 0.1, rotation: -5 },
      { x: 10 * s, duration: 0.1, rotation: 5 },
      { x: -5 * s, duration: 0.08, rotation: -2 },
      { x: 5 * s, duration: 0.08, rotation: 2 },
      { x: 0, duration: 0.1, rotation: 0 },
    ],
    ease: 'power2.inOut',
    stagger: 0.04
  })

  tl.to(chars(), { 
    y: -8 * s, 
    duration: 0.25, 
    ease: 'power2.out',
    yoyo: true,
    repeat: 2
  }, 0.5)

  tl.to(mouths(), {
    scaleX: 0.4,
    scaleY: 1.2,
    duration: 0.15,
    onComplete: () => {
      gsap.to(mouths(), { scaleX: 0.65, scaleY: 0.55, duration: 0.5, ease: 'elastic.out(1, 0.6)', delay: 0.4 })
    }
  }, 0)

  tl.to(lids(), {
    y: LID_CLOSED_Y,
    duration: 0.15,
    yoyo: true,
    repeat: 1
  }, 0.3)

  setTimeout(() => {
    currentState = 'idle'
    startIdle()
  }, 1200)
}

function successJump() {
  killIdleAndStateAnimations()
  currentState = 'success'
  const s = getScaleFactor()

  const tl = gsap.timeline()

  tl.to(chars(), { 
    y: -35 * s, 
    duration: 0.5, 
    ease: 'back.out(1.7)', 
    stagger: 0.12 
  }, 0)
  
  tl.to(chars(), { 
    y: 0, 
    duration: 0.5, 
    ease: 'bounce.out', 
    stagger: 0.12 
  }, 0.35)
  
  tl.to(chars(), { 
    rotation: 360, 
    duration: 1.2, 
    ease: 'back.out(1.5)', 
    stagger: 0.15 
  }, 0)
  
  tl.to(mouths(), { 
    scaleX: 1.3, 
    scaleY: 1.1, 
    duration: 0.5, 
    ease: 'back.out(1.5)', 
    stagger: 0.08 
  }, 0.3)

  tl.to(pupils(), {
    scale: 1.3,
    duration: 0.3,
    ease: 'back.out(1.4)',
    stagger: 0.05
  }, 0.2)
}

watch(() => props.focusState, async (newS, oldS) => {
  console.log('focusState changed:', newS, oldS)
  await nextTick()
  if (props.loginStatus === 'error') return
  if (props.loginStatus === 'success') return

  if (newS === 'email') toEmail()
  else if (newS === 'password') toPassword()
  else if (newS === 'none' && oldS !== 'none') toIdle()
})

watch(() => props.loginStatus, async (s) => {
  await nextTick()
  if (s === 'error') shake()
  if (s === 'success') successJump()
})

watch(() => props.isButtonHovered, async (h) => {
  await nextTick()
  if (props.loginStatus === 'error' || props.loginStatus === 'success') return
  h ? onHover() : onLeave()
})

onMounted(() => {
  document.addEventListener('mousemove', handleMouseMove)
  playStart()
})

onUnmounted(() => {
  document.removeEventListener('mousemove', handleMouseMove)
  if (blinkTimer) clearTimeout(blinkTimer)
  killIdleAndStateAnimations()
})
</script>

<style scoped>
.character {
  perspective: 1000px;
  transform-style: preserve-3d;
}

.face {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.circle-face {
  width: 6rem;
  height: 6rem;
  background: linear-gradient(145deg, #E8EDF5, #D1D9E6);
  border-radius: 50%;
  box-shadow:
    16px 16px 32px rgba(148, 163, 184, 0.3),
    -16px -16px 32px rgba(255, 255, 255, 0.85),
    inset 4px 4px 10px rgba(148, 163, 184, 0.2),
    inset -4px -4px 10px rgba(255, 255, 255, 0.65);
}

@media (min-width: 640px) {
  .circle-face {
    width: 8rem;
    height: 8rem;
  }
}

@media (min-width: 768px) {
  .circle-face {
    width: 10rem;
    height: 10rem;
  }
}

.square-face {
  width: 5.5rem;
  height: 5.5rem;
  background: linear-gradient(145deg, #F5E6EA, #EFD1D9);
  border-radius: 1.2rem;
  box-shadow:
    14px 14px 28px rgba(244, 114, 182, 0.25),
    -14px -14px 28px rgba(255, 255, 255, 0.8),
    inset 4px 4px 8px rgba(244, 114, 182, 0.18),
    inset -4px -4px 8px rgba(255, 255, 255, 0.6);
}

@media (min-width: 640px) {
  .square-face {
    width: 7rem;
    height: 7rem;
    border-radius: 1.4rem;
  }
}

@media (min-width: 768px) {
  .square-face {
    width: 8.8rem;
    height: 8.8rem;
    border-radius: 1.6rem;
  }
}

.capsule-face {
  width: 7rem;
  height: 4rem;
  background: linear-gradient(145deg, #E0E9F8, #C7D8F0);
  border-radius: 9999px;
  box-shadow:
    11px 11px 22px rgba(96, 165, 250, 0.22),
    -11px -11px 22px rgba(255, 255, 255, 0.8),
    inset 3px 3px 6px rgba(96, 165, 250, 0.16),
    inset -3px -3px 6px rgba(255, 255, 255, 0.55);
}

@media (min-width: 640px) {
  .capsule-face {
    width: 9rem;
    height: 5rem;
  }
}

@media (min-width: 768px) {
  .capsule-face {
    width: 11rem;
    height: 6rem;
  }
}

.eyes {
  display: flex;
  gap: 18px;
  position: relative;
  z-index: 10;
}

@media (min-width: 640px) {
  .eyes {
    gap: 26px;
  }
}

@media (min-width: 768px) {
  .eyes {
    gap: 34px;
  }
}

.eye {
  width: 16px;
  height: 16px;
  position: relative;
  overflow: hidden;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.92);
  box-shadow:
    inset 2px 2px 5px rgba(0, 0, 0, 0.08),
    0 1px 3px rgba(0, 0, 0, 0.04);
}

@media (min-width: 640px) {
  .eye {
    width: 20px;
    height: 20px;
  }
}

@media (min-width: 768px) {
  .eye {
    width: 24px;
    height: 24px;
  }
}

.pupil {
  width: 10px;
  height: 10px;
  background: radial-gradient(circle at 30% 30%, #54657B, #233142);
  border-radius: 50%;
  position: absolute;
  top: 3px;
  left: 3px;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.15);
}

@media (min-width: 640px) {
  .pupil {
    width: 12px;
    height: 12px;
    top: 4px;
    left: 4px;
  }
}

@media (min-width: 768px) {
  .pupil {
    width: 14px;
    height: 14px;
    top: 5px;
    left: 5px;
  }
}

.pupil::before {
  content: '';
  position: absolute;
  top: 2px;
  left: 2.5px;
  width: 3px;
  height: 3px;
  background: rgba(255, 255, 255, 0.85);
  border-radius: 50%;
}

@media (min-width: 640px) {
  .pupil::before {
    width: 3.5px;
    height: 3.5px;
  }
}

@media (min-width: 768px) {
  .pupil::before {
    width: 4px;
    height: 4px;
    top: 2.5px;
    left: 3.5px;
  }
}

.mouth {
  position: absolute;
  z-index: 10;
  border-radius: 9999px;
  background: linear-gradient(to bottom, rgba(30, 41, 59, 0.15), rgba(30, 41, 59, 0.1));
}

.circle-mouth {
  width: 14px;
  height: 7px;
  bottom: 24%;
}

.square-mouth {
  width: 12px;
  height: 6px;
  bottom: 20%;
}

.capsule-mouth {
  width: 10px;
  height: 5px;
  bottom: 18%;
}

@media (min-width: 640px) {
  .circle-mouth {
    width: 17px;
    height: 8.5px;
  }

  .square-mouth {
    width: 15px;
    height: 7.5px;
  }

  .capsule-mouth {
    width: 13px;
    height: 6.5px;
  }
}

@media (min-width: 768px) {
  .circle-mouth {
    width: 20px;
    height: 10px;
  }

  .square-mouth {
    width: 18px;
    height: 9px;
  }

  .capsule-mouth {
    width: 16px;
    height: 8px;
  }
}

.lid {
  position: absolute;
  overflow: hidden;
  z-index: 20;
}

.circle-lid {
  width: 6rem;
  height: 3.5rem;
  top: 0;
  left: 0;
  background: linear-gradient(to bottom, #E8EDF5, #D1D9E6);
  border-radius: 6rem 6rem 0 0;
  box-shadow:
    0 4px 8px rgba(148, 163, 184, 0.2),
    inset 0 -1.5px 3px rgba(255, 255, 255, 0.28);
}

@media (min-width: 640px) {
  .circle-lid {
    width: 8rem;
    height: 4.5rem;
    border-radius: 8rem 8rem 0 0;
  }
}

@media (min-width: 768px) {
  .circle-lid {
    width: 10rem;
    height: 5.5rem;
    border-radius: 10rem 10rem 0 0;
  }
}

.square-lid {
  width: 5.5rem;
  height: 3rem;
  top: 0;
  left: 0;
  background: linear-gradient(to bottom, #F5E6EA, #EFD1D9);
  border-radius: 1.2rem 1.2rem 0 0;
  box-shadow:
    0 3.5px 7px rgba(244, 114, 182, 0.18),
    inset 0 -1.5px 2.5px rgba(255, 255, 255, 0.32);
}

@media (min-width: 640px) {
  .square-lid {
    width: 7rem;
    height: 3.8rem;
    border-radius: 1.4rem 1.4rem 0 0;
  }
}

@media (min-width: 768px) {
  .square-lid {
    width: 8.8rem;
    height: 4.8rem;
    border-radius: 1.6rem 1.6rem 0 0;
  }
}

.capsule-lid {
  width: 7rem;
  height: 2.4rem;
  top: 0;
  left: 0;
  background: linear-gradient(to bottom, #E0E9F8, #C7D8F0);
  border-radius: 9999px 9999px 0 0;
  box-shadow:
    0 2.5px 5px rgba(96, 165, 250, 0.16),
    inset 0 -1.5px 2.5px rgba(255, 255, 255, 0.3);
}

@media (min-width: 640px) {
  .capsule-lid {
    width: 9rem;
    height: 3rem;
  }
}

@media (min-width: 768px) {
  .capsule-lid {
    width: 11rem;
    height: 3.6rem;
  }
}
</style>
