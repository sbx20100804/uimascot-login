<template>
  <div
    ref="containerRef"
    class="relative flex flex-col items-center justify-center gap-12 py-8"
    :style="{ width: '100%' }"
  >
    <div ref="circleRef" class="character-circle">
      <div ref="circleShape" class="shape shape-circle">
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
        <div ref="squareEyelid" class="eyelid eyelid-square"></div>
      </div>
    </div>

    <div ref="capsuleRef" class="character-capsule">
      <div ref="capsuleShape" class="shape shape-capsule">
        <div class="eyes-container-capsule">
          <div class="eye-line">
            <div ref="capsuleLeftPupil" class="pupil-tiny"></div>
          </div>
          <div class="eye-line">
            <div ref="capsuleRightPupil" class="pupil-tiny"></div>
          </div>
        </div>
        <div ref="capsuleEyelid" class="eyelid eyelid-capsule"></div>
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
const circleShape = ref(null)
const capsuleShape = ref(null)

const circleLeftPupil = ref(null)
const circleRightPupil = ref(null)
const squareLeftPupil = ref(null)
const squareRightPupil = ref(null)
const capsuleLeftPupil = ref(null)
const capsuleRightPupil = ref(null)

const circleEyelid = ref(null)
const squareEyelid = ref(null)
const capsuleEyelid = ref(null)

let currentState = 'idle'
let idleTimeline = null
let blinkTimers = []
let microMovementTimers = []

function playStartupAnimation() {
  const tl = gsap.timeline()

  gsap.set([circleRef.value, squareRef.value, capsuleRef.value], { y: 100, opacity: 0 })
  gsap.set([circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value, capsuleLeftPupil.value, capsuleRightPupil.value], { scale: 0 })
  gsap.set(circleEyelid.value, { y: -55 })
  gsap.set(squareEyelid.value, { y: -50 })
  gsap.set(capsuleEyelid.value, { y: -35 })

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

function blink(eyelidRef, originalY) {
  const tl = gsap.timeline()
  
  tl.to(eyelidRef, {
    y: 0,
    duration: 0.08,
    ease: 'power2.in',
  })
  .to(eyelidRef, {
    y: originalY,
    duration: 0.1,
    ease: 'power2.out',
    delay: 0.06,
  })
}

function scheduleRandomBlink() {
  const blinkDelay = Math.random() * 3000 + 1500
  
  const timer = setTimeout(() => {
    if (currentState !== 'password') {
      const random = Math.random()
      if (random < 0.6) {
        blink(circleEyelid.value, -55)
        blink(squareEyelid.value, -50)
        blink(capsuleEyelid.value, -35)
      } else if (random < 0.85) {
        blink(circleEyelid.value, -55)
        setTimeout(() => blink(squareEyelid.value, -50), 100)
        setTimeout(() => blink(capsuleEyelid.value, -35), 200)
      } else {
        blink(capsuleEyelid.value, -35)
        setTimeout(() => blink(circleEyelid.value, -55), 150)
        setTimeout(() => blink(squareEyelid.value, -50), 300)
      }
    }
    scheduleRandomBlink()
  }, blinkDelay)
  
  blinkTimers.push(timer)
}

function microPupilMovement() {
  if (currentState === 'password') return
  
  const moveDelay = Math.random() * 2000 + 1000
  
  const timer = setTimeout(() => {
    if (currentState !== 'password') {
      const offsetX = (Math.random() - 0.5) * 4
      const offsetY = (Math.random() - 0.5) * 4
      
      gsap.to([circleLeftPupil.value, circleRightPupil.value], {
        x: offsetX,
        y: offsetY,
        duration: 0.3,
        ease: 'power2.inOut',
      })
      
      gsap.to([squareLeftPupil.value, squareRightPupil.value], {
        x: offsetX * 0.8,
        y: offsetY * 0.8,
        duration: 0.35,
        ease: 'power2.inOut',
      })
      
      setTimeout(() => {
        if (currentState !== 'password') {
          gsap.to([circleLeftPupil.value, circleRightPupil.value], {
            x: 0,
            y: 0,
            duration: 0.4,
            ease: 'power2.inOut',
          })
          gsap.to([squareLeftPupil.value, squareRightPupil.value], {
            x: 0,
            y: 0,
            duration: 0.45,
            ease: 'power2.inOut',
          })
        }
      }, Math.random() * 1500 + 800)
    }
    microPupilMovement()
  }, moveDelay)
  
  microMovementTimers.push(timer)
}

function startIdleAnimation() {
  if (idleTimeline) {
    idleTimeline.kill()
  }

  idleTimeline = gsap.timeline({ repeat: -1 })

  idleTimeline
    .to(circleRef.value, {
      y: -8,
      duration: 2.5,
      ease: 'sine.inOut',
      yoyo: true,
      repeat: 1,
    })
    .to(circleShape.value, {
      scale: 1.03,
      duration: 2.5,
      ease: 'sine.inOut',
      yoyo: true,
      repeat: 1,
    }, 0)
    .to(circleRef.value, {
      x: 3,
      duration: 3,
      ease: 'sine.inOut',
      yoyo: true,
      repeat: 1,
    }, 0.5)
    .to(
      squareRef.value,
      {
        y: -6,
        duration: 2,
        ease: 'sine.inOut',
        yoyo: true,
        repeat: 1,
      },
      0.3
    )
    .to(
      squareInnerRef.value,
      {
        rotation: 2,
        duration: 2.2,
        ease: 'sine.inOut',
        yoyo: true,
        repeat: 1,
      },
      0.3
    )
    .to(
      capsuleRef.value,
      {
        y: -4,
        duration: 1.8,
        ease: 'sine.inOut',
        yoyo: true,
        repeat: 1,
      },
      0.6
    )
    .to(
      capsuleShape.value,
      {
        scaleX: 1.02,
        duration: 1.8,
        ease: 'sine.inOut',
        yoyo: true,
        repeat: 1,
      },
      0.6
    )
    .to(
      capsuleRef.value,
      {
        x: -2,
        duration: 2.8,
        ease: 'sine.inOut',
        yoyo: true,
        repeat: 1,
      },
      0.9
    )
}

function stopIdleAnimation() {
  if (idleTimeline) {
    idleTimeline.kill()
    idleTimeline = null
  }
}

function stopBlinkTimers() {
  blinkTimers.forEach(timer => clearTimeout(timer))
  blinkTimers = []
}

function stopMicroMovementTimers() {
  microMovementTimers.forEach(timer => clearTimeout(timer))
  microMovementTimers = []
}

function transitionToEmailFocus() {
  stopIdleAnimation()
  stopMicroMovementTimers()
  currentState = 'email'

  const tl = gsap.timeline()

  tl.to([circleRef.value, squareRef.value, capsuleRef.value], {
    x: 18,
    rotation: 3,
    duration: 0.35,
    ease: 'power2.out',
  })
  .to(
    [circleShape.value, squareInnerRef.value, capsuleShape.value],
    {
      scaleY: [1, 0.96, 1.04, 1],
      duration: 0.45,
      ease: 'power2.inOut',
    },
    '-=0.15'
  )
  .to(
    [circleLeftPupil.value, circleRightPupil.value, squareLeftPupil.value, squareRightPupil.value],
    {
      x: 2,
      duration: 0.3,
      ease: 'power2.out',
    },
    '-=0.3'
  )

  return tl
}

function transitionToPasswordFocus() {
  stopIdleAnimation()
  stopMicroMovementTimers()
  currentState = 'password'

  const tl = gsap.timeline()

  tl.to([circleRef.value, squareRef.value, capsuleRef.value], {
    x: 0,
    rotation: 0,
    duration: 0.25,
    ease: 'power2.out',
  })

  tl.to([circleLeftPupil.value, circleRightPupil.value], {
    y: -35,
    x: 0,
    duration: 0.2,
    ease: 'power2.in',
  }, '-=0.1')
  .to(circleEyelid.value, {
    y: 0,
    duration: 0.3,
    ease: 'power2.out',
  })

  tl.to([squareLeftPupil.value, squareRightPupil.value], {
    y: -30,
    x: 0,
    duration: 0.2,
    ease: 'power2.in',
  }, '-=0.25')
  .to(squareEyelid.value, {
    y: 0,
    duration: 0.3,
    ease: 'power2.out',
  }, '-=0.25')

  tl.to(squareInnerRef.value, {
    rotateY: 180,
    duration: 0.6,
    ease: 'power2.inOut',
  }, '-=0.35')

  tl.to(capsuleRef.value, {
    scaleY: 0.3,
    duration: 0.35,
    ease: 'power2.inOut',
  }, '-=0.25')

  tl.to(capsuleEyelid.value, {
    y: 0,
    duration: 0.3,
    ease: 'power2.out',
  }, '-=0.25')

  return tl
}

function transitionToIdle() {
  currentState = 'idle'

  const tl = gsap.timeline()

  tl.to([circleRef.value, squareRef.value, capsuleRef.value], {
    x: 0,
    rotation: 0,
    y: 0,
    duration: 0.35,
    ease: 'power2.out',
  })

  tl.to(circleEyelid.value, {
    y: -55,
    duration: 0.25,
    ease: 'power2.out',
  }, '-=0.15')
  .to([circleLeftPupil.value, circleRightPupil.value], {
    y: 0,
    x: 0,
    duration: 0.25,
    ease: 'power2.out',
  }, '-=0.15')

  tl.to(squareEyelid.value, {
    y: -50,
    duration: 0.25,
    ease: 'power2.out',
  }, '-=0.2')
  .to([squareLeftPupil.value, squareRightPupil.value], {
    y: 0,
    x: 0,
    duration: 0.25,
    ease: 'power2.out',
  }, '-=0.2')

  tl.to(squareInnerRef.value, {
    rotateY: 0,
    duration: 0.6,
    ease: 'power2.inOut',
  }, '-=0.35')

  tl.to(capsuleRef.value, {
    scaleY: 1,
    duration: 0.35,
    ease: 'power2.inOut',
  }, '-=0.25')

  tl.to(capsuleEyelid.value, {
    y: -35,
    duration: 0.25,
    ease: 'power2.out',
  }, '-=0.3')

  tl.call(() => {
    startIdleAnimation()
    microPupilMovement()
  })

  return tl
}

function handleButtonHover() {
  if (currentState === 'password') {
    gsap.to([circleLeftPupil.value, circleRightPupil.value], {
      y: -10,
      duration: 0.25,
      ease: 'power2.out',
    })
    gsap.to(circleEyelid.value, {
      y: -22,
      duration: 0.25,
      ease: 'power2.out',
    })
    gsap.to([squareLeftPupil.value, squareRightPupil.value], {
      y: -8,
      duration: 0.25,
      ease: 'power2.out',
    })
    gsap.to(squareEyelid.value, {
      y: -20,
      duration: 0.25,
      ease: 'power2.out',
    })
    gsap.to(capsuleRef.value, {
      scaleY: 0.5,
      duration: 0.25,
      ease: 'power2.out',
    })
    gsap.to(capsuleEyelid.value, {
      y: -15,
      duration: 0.25,
      ease: 'power2.out',
    })
  }

  gsap.to([circleRef.value, squareRef.value, capsuleRef.value], {
    y: currentState === 'email' ? -18 : -12,
    duration: 0.4,
    ease: 'elastic.out(1, 0.35)',
  })
  
  gsap.to([circleShape.value, capsuleShape.value], {
    scale: 1.05,
    duration: 0.35,
    ease: 'power2.out',
  })
}

function handleButtonLeave() {
  if (currentState === 'password') {
    gsap.to([circleLeftPupil.value, circleRightPupil.value], {
      y: -35,
      duration: 0.18,
      ease: 'power2.in',
    })
    gsap.to(circleEyelid.value, {
      y: 0,
      duration: 0.18,
      ease: 'power2.in',
    })
    gsap.to([squareLeftPupil.value, squareRightPupil.value], {
      y: -30,
      duration: 0.18,
      ease: 'power2.in',
    })
    gsap.to(squareEyelid.value, {
      y: 0,
      duration: 0.18,
      ease: 'power2.in',
    })
    gsap.to(capsuleRef.value, {
      scaleY: 0.3,
      duration: 0.18,
      ease: 'power2.in',
    })
    gsap.to(capsuleEyelid.value, {
      y: 0,
      duration: 0.18,
      ease: 'power2.in',
    })
  }

  gsap.to(circleRef.value, {
    y: currentState === 'email' ? -8 : 0,
    duration: 0.25,
    ease: 'power2.out',
  })
  gsap.to(squareRef.value, {
    y: currentState === 'email' ? -6 : 0,
    duration: 0.25,
    ease: 'power2.out',
  })
  gsap.to(capsuleRef.value, {
    y: currentState === 'email' ? -4 : 0,
    duration: 0.25,
    ease: 'power2.out',
  })
  
  gsap.to([circleShape.value, capsuleShape.value], {
    scale: 1,
    duration: 0.3,
    ease: 'power2.out',
  })
}

function triggerErrorShake() {
  gsap.to([circleRef.value, squareRef.value, capsuleRef.value], {
    keyframes: [
      { x: -18, duration: 0.07, rotation: -10 },
      { x: 18, duration: 0.07, rotation: 10 },
      { x: -14, duration: 0.055, rotation: -7 },
      { x: 14, duration: 0.055, rotation: 7 },
      { x: -10, duration: 0.045, rotation: -5 },
      { x: 10, duration: 0.045, rotation: 5 },
      { x: 0, duration: 0.07, rotation: 0 },
    ],
    ease: 'power2.inOut',
  })
  
  gsap.to([circleEyelid.value, squareEyelid.value, capsuleEyelid.value], {
    y: 0,
    duration: 0.1,
    ease: 'power2.out',
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
  gsap.set(squareEyelid.value, { y: -50 })
  gsap.set(capsuleEyelid.value, { y: -35 })

  playStartupAnimation().then(() => {
    startIdleAnimation()
    scheduleRandomBlink()
    microPupilMovement()
  })
})

onUnmounted(() => {
  stopIdleAnimation()
  stopBlinkTimers()
  stopMicroMovementTimers()
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
  overflow: hidden;
  z-index: 20;
}

.eyelid-circle {
  width: 8rem;
  height: 4.5rem;
  top: 0;
  left: 0;
  background-color: #E2E8F0;
  border-radius: 8rem 8rem 0 0;
  box-shadow: 0 4px 8px rgba(148, 163, 184, 0.2);
}

.eyelid-square {
  width: 7rem;
  height: 4rem;
  top: 0;
  left: 0;
  background-color: #FEE2E2;
  border-radius: 1.5rem 1.5rem 0 0;
  box-shadow: 0 3px 6px rgba(248, 113, 113, 0.15);
}

.eyelid-capsule {
  width: 10rem;
  height: 3rem;
  top: 0;
  left: 0;
  background-color: #DBEAFE;
  border-radius: 9999px 9999px 0 0;
  box-shadow: 0 2px 4px rgba(96, 165, 250, 0.1);
}
</style>
