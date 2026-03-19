<template>
  <div
    ref="containerRef"
    class="relative flex flex-col items-center justify-center gap-12 py-8"
    :style="{ width: '100%' }"
  >
    <div ref="circleRef" class="character-circle">
      <div class="shape shape-circle">
        <div class="eyes-container-circle">
          <div class="eye-orbit">
            <div ref="circleLeftPupil" class="pupil-large"></div>
          </div>
          <div class="eye-orbit">
            <div ref="circleRightPupil" class="pupil-large"></div>
          </div>
        </div>
        <div ref="circleEyelid" class="eyelid eyelid-circle"></div>
      </div>
    </div>

    <div ref="squareRef" class="character-square">
      <div ref="squareInner" class="shape shape-square">
        <div class="eyes-container-square">
          <div class="eye-orbit-small">
            <div ref="squareLeftPupil" class="pupil-oval"></div>
          </div>
          <div class="eye-orbit-small">
            <div ref="squareRightPupil" class="pupil-oval"></div>
          </div>
        </div>
      </div>
    </div>

    <div ref="capsuleRef" class="character-capsule">
      <div class="shape shape-capsule">
        <div class="eyes-container-capsule">
          <div class="eye-line">
            <div ref="capsuleLeftPupil" class="pupil-tiny"></div>
          </div>
          <div class="eye-line">
            <div ref="capsuleRightPupil" class="pupil-tiny"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'
import gsap from 'gsap'

const props = defineProps({
  focusState: {
    type: String,
    default: 'none',
  },
  loginStatus: {
    type: String,
    default: 'idle',
  },
  isButtonHovered: {
    type: Boolean,
    default: false,
  },
})

const containerRef = ref(null)
const circleRef = ref(null)
const squareRef = ref(null)
const squareInnerRef = ref(null)
const capsuleRef = ref(null)

const circleLeftPupil = ref(null)
const circleRightPupil = ref(null)
const squareLeftPupil = ref(null)
const squareRightPupil = ref(null)
const capsuleLeftPupil = ref(null)
const capsuleRightPupil = ref(null)

const circleEyelid = ref(null)

let currentState = 'idle'
let idleTimeline = null

function playStartupAnimation() {
  const tl = gsap.timeline()

  gsap.set([circleRef.value, squareRef.value, capsuleRef.value], { y: 100, opacity: 0 })
  gsap.set([circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value, capsuleLeftPupil.value, capsuleRightPupil.value], { scale: 0 })

  tl.to([circleRef.value, squareRef.value, capsuleRef.value], {
    opacity: 1,
    duration: 0.6,
    ease: 'power2.out',
  })
  .to([circleRef.value, squareRef.value, capsuleRef.value], {
    y: 0,
    duration: 0.8,
    stagger: 0.2,
    ease: 'elastic.out(1, 0.4)',
  }, '-=0.4')
  .to([circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value, capsuleLeftPupil.value, capsuleRightPupil.value], {
    scale: 1,
    duration: 0.3,
    ease: 'back.out(1.7)',
  }, '-=0.3')
  .to([circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value, capsuleLeftPupil.value, capsuleRightPupil.value], {
    scale: 1.1,
    duration: 0.15,
    ease: 'power2.out',
    yoyo: true,
    repeat: 1,
  }, '-=0.1')

  return tl
}

function startIdleAnimation() {
  if (idleTimeline) {
    idleTimeline.kill()
  }

  idleTimeline = gsap.timeline({ repeat: -1 })

  idleTimeline
    .to(circleRef.value, {
      y: -6,
      duration: 3,
      ease: 'sine.inOut',
      yoyo: true,
      repeat: 1,
    })
    .to(
      squareRef.value,
      {
        y: -4,
        duration: 2.5,
        ease: 'sine.inOut',
        yoyo: true,
        repeat: 1,
      },
      0.5
    )
    .to(
      capsuleRef.value,
      {
        y: -3,
        duration: 2,
        ease: 'sine.inOut',
        yoyo: true,
        repeat: 1,
      },
      1
    )
}

function stopIdleAnimation() {
  if (idleTimeline) {
    idleTimeline.kill()
    idleTimeline = null
  }
}

function transitionToEmailFocus() {
  stopIdleAnimation()
  currentState = 'email'

  const tl = gsap.timeline()

  tl.to([circleRef.value, squareRef.value, capsuleRef.value], {
    x: 15,
    rotation: 5,
    duration: 0.4,
    ease: 'power2.out',
  })
  .to(
    [circleRef.value, squareRef.value, capsuleRef.value],
    {
      scaleY: [1, 0.95, 1.05, 1],
      duration: 0.5,
      ease: 'power2.inOut',
    },
    '-=0.2'
  )

  return tl
}

function transitionToPasswordFocus() {
  stopIdleAnimation()
  currentState = 'password'

  const tl = gsap.timeline()

  tl.to([circleRef.value, squareRef.value, capsuleRef.value], {
    x: 0,
    rotation: 0,
    duration: 0.3,
    ease: 'power2.out',
  })

  tl.to([circleLeftPupil.value, circleRightPupil.value], {
    y: -35,
    duration: 0.25,
    ease: 'power2.in',
  }, '-=0.1')
  .to(circleEyelid.value, {
    y: 0,
    duration: 0.35,
    ease: 'power2.out',
  })

  tl.to(squareInnerRef.value, {
    rotateY: 180,
    duration: 0.7,
    ease: 'power2.inOut',
  }, '-=0.4')

  tl.to(capsuleRef.value, {
    scaleY: 0.3,
    duration: 0.4,
    ease: 'power2.inOut',
  }, '-=0.3')

  return tl
}

function transitionToIdle() {
  currentState = 'idle'

  const tl = gsap.timeline()

  tl.to([circleRef.value, squareRef.value, capsuleRef.value], {
    x: 0,
    rotation: 0,
    y: 0,
    duration: 0.4,
    ease: 'power2.out',
  })

  tl.to(circleEyelid.value, {
    y: -55,
    duration: 0.3,
    ease: 'power2.out',
  }, '-=0.2')
  .to([circleLeftPupil.value, circleRightPupil.value], {
    y: 0,
    duration: 0.3,
    ease: 'power2.out',
  }, '-=0.2')

  tl.to(squareInnerRef.value, {
    rotateY: 0,
    duration: 0.7,
    ease: 'power2.inOut',
  }, '-=0.4')

  tl.to(capsuleRef.value, {
    scaleY: 1,
    duration: 0.4,
    ease: 'power2.inOut',
  }, '-=0.3')

  tl.call(() => {
    startIdleAnimation()
  })

  return tl
}

function handleButtonHover() {
  if (currentState === 'password') {
    gsap.to([circleLeftPupil.value, circleRightPupil.value], {
      y: -12,
      duration: 0.3,
      ease: 'power2.out',
    })
    gsap.to(circleEyelid.value, {
      y: -20,
      duration: 0.3,
      ease: 'power2.out',
    })
    gsap.to(capsuleRef.value, {
      scaleY: 0.5,
      duration: 0.3,
      ease: 'power2.out',
    })
  }

  gsap.to([circleRef.value, squareRef.value, capsuleRef.value], {
    y: currentState === 'email' ? -15 : -10,
    duration: 0.5,
    ease: 'elastic.out(1, 0.3)',
  })
}

function handleButtonLeave() {
  if (currentState === 'password') {
    gsap.to([circleLeftPupil.value, circleRightPupil.value], {
      y: -35,
      duration: 0.2,
      ease: 'power2.in',
    })
    gsap.to(circleEyelid.value, {
      y: 0,
      duration: 0.2,
      ease: 'power2.in',
    })
    gsap.to(capsuleRef.value, {
      scaleY: 0.3,
      duration: 0.2,
      ease: 'power2.in',
    })
  }

  gsap.to(circleRef.value, {
    y: currentState === 'email' ? -6 : 0,
    duration: 0.3,
    ease: 'power2.out',
  })
  gsap.to(squareRef.value, {
    y: currentState === 'email' ? -4 : 0,
    duration: 0.3,
    ease: 'power2.out',
  })
  gsap.to(capsuleRef.value, {
    y: currentState === 'email' ? -3 : 0,
    duration: 0.3,
    ease: 'power2.out',
  })
}

function triggerErrorShake() {
  gsap.to([circleRef.value, squareRef.value, capsuleRef.value], {
    keyframes: [
      { x: -15, duration: 0.08, rotation: -8 },
      { x: 15, duration: 0.08, rotation: 8 },
      { x: -12, duration: 0.06, rotation: -6 },
      { x: 12, duration: 0.06, rotation: 6 },
      { x: -8, duration: 0.05, rotation: -4 },
      { x: 8, duration: 0.05, rotation: 4 },
      { x: 0, duration: 0.08, rotation: 0 },
    ],
    ease: 'power2.inOut',
  })
}

watch(
  () => props.focusState,
  (newState, oldState) => {
    if (props.loginStatus === 'error') return

    if (newState === 'email') {
      transitionToEmailFocus()
    } else if (newState === 'password') {
      transitionToPasswordFocus()
    } else if (newState === 'none' && oldState !== 'none') {
      transitionToIdle()
    }
  }
)

watch(
  () => props.loginStatus,
  (newStatus) => {
    if (newStatus === 'error') {
      triggerErrorShake()
    }
  }
)

watch(
  () => props.isButtonHovered,
  (isHovered) => {
    if (isHovered) {
      handleButtonHover()
    } else {
      handleButtonLeave()
    }
  }
)

onMounted(() => {
  gsap.set(circleEyelid.value, { y: -55 })

  playStartupAnimation().then(() => {
    startIdleAnimation()
  })
})

onUnmounted(() => {
  stopIdleAnimation()
})
</script>

<style scoped>
.character-circle,
.character-square,
.character-capsule {
  perspective: 1200px;
}

.shape {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.shape-circle {
  width: 8rem;
  height: 8rem;
  background-color: #E2E8F0;
  border-radius: 50%;
  box-shadow:
    12px 12px 24px rgba(148, 163, 184, 0.3),
    -12px -12px 24px rgba(255, 255, 255, 0.9),
    inset 4px 4px 8px rgba(148, 163, 184, 0.2),
    inset -4px -4px 8px rgba(255, 255, 255, 0.7);
}

.shape-square {
  width: 7rem;
  height: 7rem;
  background-color: #FEE2E2;
  border-radius: 1.5rem;
  backface-visibility: hidden;
  box-shadow:
    10px 10px 20px rgba(248, 113, 113, 0.25),
    -10px -10px 20px rgba(255, 255, 255, 0.85),
    inset 3px 3px 6px rgba(248, 113, 113, 0.15),
    inset -3px -3px 6px rgba(255, 255, 255, 0.6);
}

.shape-capsule {
  width: 10rem;
  height: 5rem;
  background-color: #DBEAFE;
  border-radius: 9999px;
  box-shadow:
    6px 6px 12px rgba(96, 165, 250, 0.15),
    -6px -6px 12px rgba(255, 255, 255, 0.8),
    inset 2px 2px 4px rgba(96, 165, 250, 0.1),
    inset -2px -2px 4px rgba(255, 255, 255, 0.5);
}

.eyes-container-circle {
  display: flex;
  gap: 28px;
  position: relative;
  z-index: 10;
}

.eyes-container-square {
  display: flex;
  gap: 16px;
  position: relative;
  z-index: 10;
}

.eyes-container-capsule {
  display: flex;
  gap: 32px;
  position: relative;
  z-index: 10;
}

.eye-orbit {
  width: 20px;
  height: 20px;
  position: relative;
  overflow: hidden;
  border-radius: 50%;
  box-shadow: inset 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.eye-orbit-small {
  width: 16px;
  height: 18px;
  position: relative;
  overflow: hidden;
  border-radius: 50%;
  box-shadow: inset 1.5px 1.5px 3px rgba(0, 0, 0, 0.08);
}

.pupil-large {
  width: 14px;
  height: 14px;
  background-color: #1E293B;
  border-radius: 50%;
  position: absolute;
  top: 3px;
  left: 3px;
}

.pupil-oval {
  width: 10px;
  height: 14px;
  background-color: #1E293B;
  border-radius: 50%;
  position: absolute;
  top: 2px;
  left: 3px;
}

.eye-line {
  width: 16px;
  height: 4px;
  background-color: #94A3B8;
  border-radius: 2px;
  position: relative;
  overflow: hidden;
}

.pupil-tiny {
  width: 3px;
  height: 3px;
  background-color: #1E293B;
  border-radius: 50%;
  position: absolute;
  top: 0.5px;
  left: 6.5px;
}

.eyelid {
  position: absolute;
  background-color: #E2E8F0;
  overflow: hidden;
}

.eyelid-circle {
  width: 8rem;
  height: 4.5rem;
  top: 0;
  left: 0;
  border-radius: 8rem 8rem 0 0;
  z-index: 20;
  box-shadow: 0 4px 8px rgba(148, 163, 184, 0.2);
}
</style>
