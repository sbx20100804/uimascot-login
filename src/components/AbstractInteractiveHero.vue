<template>
  <div ref="containerRef" class="relative flex flex-col items-center justify-center gap-10 py-8">
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
        <div ref="circleMouth" class="mouth circle-mouth"></div>
        <div ref="circleLid" class="lid circle-lid"></div>
      </div>
    </div>

    <div ref="squareRef" class="character">
      <div class="face square-face">
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
}

function startIdle() {
  idleTweens.forEach(t => t.kill())
  idleTweens = []
  idleTweens.push(
    gsap.to(circleRef.value, { y: -6, duration: 3, ease: 'sine.inOut', yoyo: true, repeat: -1 }),
    gsap.to(squareRef.value, { y: -5, duration: 2.8, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 0.3 }),
    gsap.to(capsuleRef.value, { y: -4, duration: 2.5, ease: 'sine.inOut', yoyo: true, repeat: -1, delay: 0.6 })
  )
}

function handleMouseMove(e) {
  if (currentState === 'password') return
  if (!containerRef.value) return

  const rect = containerRef.value.getBoundingClientRect()
  const cx = rect.left + rect.width / 2
  const cy = rect.top + rect.height / 2
  const dx = (e.clientX - cx) / (window.innerWidth / 2)
  const dy = (e.clientY - cy) / (window.innerHeight / 2)

  const maxOffset = currentState === 'email' ? 3 : 6
  const cX = dx * maxOffset
  const cY = dy * maxOffset
  const sX = dx * (maxOffset - 1)
  const sY = dy * (maxOffset - 1)
  const capX = dx * (maxOffset - 2)
  const capY = dy * (maxOffset - 2)

  gsap.to([circleLeftPupil.value, circleRightPupil.value], { x: cX, y: cY, duration: 0.25, ease: 'power1.out', overwrite: true })
  gsap.to([squareLeftPupil.value, squareRightPupil.value], { x: sX, y: sY, duration: 0.25, ease: 'power1.out', overwrite: true })
  gsap.to([capsuleLeftPupil.value, capsuleRightPupil.value], { x: capX, y: capY, duration: 0.25, ease: 'power1.out', overwrite: true })
}

function blink() {
  if (currentState === 'password') return
  const tl = gsap.timeline()
  tl.to(lids(), { y: LID_CLOSED_Y, duration: 0.08, ease: 'power2.in' })
  tl.to(lids(), { y: LID_OPEN_Y, duration: 0.12, ease: 'power2.out', delay: 0.06 })
}

function scheduleBlink() {
  if (blinkTimer) clearTimeout(blinkTimer)
  blinkTimer = setTimeout(() => {
    if (currentState !== 'password') blink()
    scheduleBlink()
  }, Math.random() * 3000 + 2000)
}

function playStart() {
  killIdleAndStateAnimations()

  gsap.set(chars(), { y: 80, opacity: 0, scale: 0.5 })
  gsap.set(pupils(), { scale: 1 })
  gsap.set(mouths(), { scaleX: 0, scaleY: 0 })
  gsap.set(lids(), { y: LID_CLOSED_Y })

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
    .to(chars(), { opacity: 1, duration: 0.5, ease: 'power2.out' })
    .to(chars(), { y: 0, scale: 1, duration: 0.8, stagger: 0.12, ease: 'back.out(1.5)' }, '-=0.2')
    .to(lids(), { y: LID_OPEN_Y, duration: 0.3, ease: 'back.out(1.5)', stagger: 0.05 }, '-=0.2')
    .to(mouths(), { scaleX: 0.7, scaleY: 0.6, duration: 0.3, ease: 'back.out(1)' }, '-=0.2')
}

function toEmail() {
  killIdleAndStateAnimations()
  currentState = 'email'

  const tl = gsap.timeline()
  tl.to(chars(), { x: 10, rotation: 1.5, y: 0, duration: 0.35, ease: 'power2.out' })
  tl.to(lids(), { y: LID_OPEN_Y, duration: 0.3, ease: 'back.out(1.5)' }, '-=0.2')
  tl.to(mouths(), { scaleX: 0.7, scaleY: 0.6, duration: 0.3, ease: 'power2.out' }, '-=0.2')
}

function toPassword() {
  killIdleAndStateAnimations()
  currentState = 'password'

  gsap.set(pupils(), { x: 0, y: 0 })

  const tl = gsap.timeline()
  tl.to(chars(), { x: 0, rotation: 0, y: 0, duration: 0.3, ease: 'power2.out' })
  tl.to(lids(), { y: LID_CLOSED_Y, duration: 0.2, ease: 'power2.in' }, '-=0.1')
  tl.to(mouths(), { scaleX: 0.3, scaleY: 0.2, duration: 0.25, ease: 'power2.out' }, '-=0.15')
}

function toIdle() {
  killIdleAndStateAnimations()
  currentState = 'idle'

  const tl = gsap.timeline()
  tl.to(chars(), { x: 0, rotation: 0, y: 0, duration: 0.35, ease: 'power2.out' })
  tl.to(lids(), { y: LID_OPEN_Y, duration: 0.3, ease: 'back.out(1.5)' }, '-=0.1')
  tl.to(mouths(), { scaleX: 0.7, scaleY: 0.6, duration: 0.3, ease: 'power2.out' }, '-=0.2')
  tl.to(pupils(), { x: 0, y: 0, duration: 0.35, ease: 'power2.out' }, 0)
  tl.add(() => {
    if (currentState === 'idle') startIdle()
  })
}

function onHover() {
  if (currentState === 'password') return
  idleTweens.forEach(t => t.kill())
  idleTweens = []

  const tl = gsap.timeline()
  tl.to(chars(), { y: -10, duration: 0.3, ease: 'power2.out' })
  tl.to(mouths(), { scaleX: 1, scaleY: 1, duration: 0.3, ease: 'power2.out' }, '-=0.15')
}

function onLeave() {
  if (currentState === 'password') return

  const tl = gsap.timeline()
  tl.to(chars(), { y: 0, duration: 0.3, ease: 'power2.out' })
  tl.to(mouths(), { scaleX: 0.7, scaleY: 0.6, duration: 0.3, ease: 'power2.out' }, '-=0.15')
  tl.add(() => {
    if (currentState === 'idle') startIdle()
  })
}

function shake() {
  killIdleAndStateAnimations()
  currentState = 'error'

  gsap.to(chars(), {
    keyframes: [
      { x: -12, duration: 0.07, rotation: -6 },
      { x: 12, duration: 0.07, rotation: 6 },
      { x: -8, duration: 0.05, rotation: -4 },
      { x: 8, duration: 0.05, rotation: 4 },
      { x: 0, duration: 0.06, rotation: 0 },
    ],
    ease: 'power2.inOut',
  })

  gsap.to(mouths(), { scaleX: 1.2, scaleY: 0.8, duration: 0.1, onComplete: () => {
    gsap.to(mouths(), { scaleX: 0.7, scaleY: 0.6, duration: 0.3, ease: 'back.out(1)', delay: 0.3 })
  }})

  setTimeout(() => {
    currentState = 'idle'
    startIdle()
  }, 500)
}

watch(() => props.focusState, async (newS, oldS) => {
  await nextTick()
  if (props.loginStatus === 'error') return

  if (newS === 'email') toEmail()
  else if (newS === 'password') toPassword()
  else if (newS === 'none' && oldS !== 'none') toIdle()
})

watch(() => props.loginStatus, async (s) => {
  await nextTick()
  if (s === 'error') shake()
})

watch(() => props.isButtonHovered, async (h) => {
  await nextTick()
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

.mouth {
  position: absolute;
  z-index: 10;
  border-radius: 9999px;
  background: rgba(30, 41, 59, 0.15);
}

.circle-mouth {
  width: 20px;
  height: 10px;
  bottom: 28%;
}

.square-mouth {
  width: 18px;
  height: 9px;
  bottom: 24%;
}

.capsule-mouth {
  width: 16px;
  height: 8px;
  bottom: 22%;
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
