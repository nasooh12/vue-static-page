G<template>
  <main class="wrap" :class="{ glow: moonGlow }">
    <!-- 은은한 구름 -->
    <div class="cloud c1"></div>
    <div class="cloud c2"></div>
    <div class="cloud c3"></div>

    <!-- 헤더 -->
    <header class="header">
      <h1>🌕 풍요로운 한가위 되세요</h1>
      <p class="subtitle">가족과 함께하는 따뜻한 시간, 풍성한 보름달처럼 행복이 가득하길 바랍니다.</p>
    </header>

    <!-- 메인 비주얼: 달 + 연등 -->
    <section class="hero">
      <svg class="moon" viewBox="0 0 300 300" role="img" aria-label="한가위 보름달">
        <defs>
          <radialGradient id="g" cx="50%" cy="45%" r="60%">
            <stop offset="0%" stop-color="#fff7cc" />
            <stop offset="60%" stop-color="#ffe27a" />
            <stop offset="100%" stop-color="#f6c65b" />
          </radialGradient>
          <filter id="softGlow">
            <feGaussianBlur stdDeviation="12" result="blur" />
            <feMerge>
              <feMergeNode in="blur" />
              <feMergeNode in="SourceGraphic" />
            </feMerge>
          </filter>
        </defs>

        <!-- 달 -->
        <circle
            cx="150"
            cy="150"
            r="110"
            fill="url(#g)"
            :filter="moonGlow ? 'url(#softGlow)' : undefined"
        />

        <!-- 달토끼 실루엣 -->
        <g class="rabbit" fill="#e9b65a" opacity="0.9" transform="translate(150,165)">
          <ellipse rx="26" ry="20" />
          <ellipse rx="14" ry="18" transform="translate(-16,-22) rotate(-10)" />
          <ellipse rx="14" ry="18" transform="translate(6,-24) rotate(15)" />
          <circle r="6" transform="translate(20,6)" />
          <ellipse rx="15" ry="6" transform="translate(0,22)" />
        </g>

        <!-- 달 표면 얼룩 -->
        <g class="craters" fill="#f0bd67" opacity="0.35">
          <circle cx="190" cy="120" r="10" />
          <circle cx="115" cy="100" r="8" />
          <circle cx="165" cy="180" r="7" />
          <circle cx="120" cy="170" r="6" />
        </g>
      </svg>

      <!-- 떠오르는 연등 (서브컴포넌트 없이 v-for로 생성) -->
      <div class="lanterns" aria-hidden="true">
        <div
            v-for="lan in lanterns"
            :key="lan.id"
            class="lantern"
            :style="{ left: lan.x + 'vw', animationDelay: lan.delay + 's' }"
        >
          <svg viewBox="0 0 60 100" class="lantern-svg">
            <defs>
              <linearGradient id="lg" x1="0" x2="0" y1="0" y2="1">
                <stop offset="0%" stop-color="#ffb67a" />
                <stop offset="100%" stop-color="#ff7e4d" />
              </linearGradient>
            </defs>
            <rect x="10" y="20" width="40" height="55" rx="14" fill="url(#lg)" />
            <rect x="18" y="12" width="24" height="10" rx="4" fill="#c4512d" />
            <rect x="18" y="77" width="24" height="8" rx="4" fill="#c4512d" />
            <circle cx="30" cy="55" r="5" fill="#ffdba5" />
            <path d="M30 85 L25 98 L35 98 Z" fill="#ffcf70" />
          </svg>
        </div>
      </div>
    </section>

    <!-- 인터랙션 -->
    <section class="panel">
      <button class="btn" @click="toggleGlow">
        {{ moonGlow ? '달빛 살짝 줄이기' : '달빛 밝게 켜기' }}
      </button>
      <button class="btn outline" @click="nextWish">복 기원 한마디 받기</button>
      <p class="wish" v-if="currentWish">“{{ currentWish }}”</p>
    </section>

    <!-- 푸터 -->
    <footer class="footer">
      <small>© {{ year }} 한가위 웹 · 제작: 당신</small>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

/** 달빛 토글 */
const moonGlow = ref(true)
const toggleGlow = () => (moonGlow.value = !moonGlow.value)

/** 연등 데이터 (JSX/서브컴포넌트 없이 렌더) */
const lanterns = Array.from({ length: 5 }, (_, i) => ({
  id: i + 1,
  delay: (i + 1) * 2,
  x: i * 20 + 5
}))

/** 복 기원 문구 */
const wishes = [
  '보름달처럼 마음까지 가득 채워지는 추석 되세요',
  '가족과 웃음꽃 피는 연휴 보내세요',
  '건강과 평안이 늘 함께하길 바랍니다',
  '새로운 시작에 행운이 가득하길!',
  '송편처럼 달콤한 일만 가득하길 🍡'
]
const idx = ref(0)
const currentWish = computed(() => wishes[idx.value] ?? '')
const nextWish = () => (idx.value = (idx.value + 1) % wishes.length)

/** 연도 */
const year = new Date().getFullYear()
</script>

<style scoped>
:root {
  --bg-top: #0b1020;
  --bg-bottom: #1a2545;
  --accent: #ffd36b;
  --text: #f7f7f7;
  --muted: #cfd6ff;
}

* { box-sizing: border-box; }

.wrap {
  min-height: 100svh;
  display: grid;
  grid-template-rows: auto 1fr auto;
  gap: 1.5rem;
  padding: clamp(16px, 3vw, 40px);
  color: var(--text);
  background: linear-gradient(180deg, var(--bg-top), var(--bg-bottom));
  position: relative;
  overflow: clip;
}

/* 달 글로우 토글 */
.wrap.glow .moon {
  filter: drop-shadow(0 0 18px rgba(255, 220, 100, 0.55));
}

.header { text-align: center; }
.header h1 {
  margin: 0 0 .25rem;
  font-size: clamp(1.6rem, 2.8vw, 2.4rem);
  letter-spacing: .02em;
}
.subtitle {
  margin: 0;
  color: var(--muted);
  font-size: clamp(.95rem, 1.6vw, 1.05rem);
}

.hero {
  display: grid;
  place-items: center;
  position: relative;
  margin-block: .5rem;
}

.moon {
  width: min(68vw, 520px);
  max-width: 92vh;
  transition: filter .4s ease;
}

/* 떠오르는 연등 */
.lanterns {
  position: absolute;
  inset: 0;
  pointer-events: none;
}
.lantern {
  position: absolute;
  bottom: -15vh;
  animation: floatUp 16s linear infinite;
  opacity: .9;
}
.lantern-svg {
  width: clamp(36px, 6vw, 70px);
  filter: drop-shadow(0 6px 18px rgba(255, 120, 64, .25));
}
@keyframes floatUp {
  0%   { transform: translateY(0) translateX(0); opacity: 0; }
  5%   { opacity: .95; }
  50%  { transform: translateY(-60vh) translateX(2vw); }
  100% { transform: translateY(-110vh) translateX(-2vw); opacity: 0; }
}

/* 구름 */
.cloud {
  position: absolute;
  background: radial-gradient(ellipse at center, rgba(255,255,255,.14), rgba(255,255,255,0) 60%);
  width: 40vw; height: 20vw; filter: blur(6px);
  opacity: .25; border-radius: 50%;
}
.c1 { top: 8vh; left: -10vw; animation: drift 38s linear infinite; }
.c2 { top: 20vh; right: -12vw; animation: drift 46s linear infinite reverse; }
.c3 { top: 34vh; left: -8vw; animation: drift 52s linear infinite; }
@keyframes drift { to { transform: translateX(30vw); } }

/* 패널 */
.panel {
  display: grid;
  justify-items: center;
  gap: .75rem;
  text-align: center;
}
.btn {
  appearance: none;
  border: 0;
  border-radius: 999px;
  padding: .85rem 1.2rem;
  font-size: 0.98rem;
  font-weight: 700;
  background: linear-gradient(90deg, #ffd36b, #ffb65b);
  color: #3c2a00;
  box-shadow:
      0 6px 14px rgba(255, 196, 66, .25),
      inset 0 -2px 0 rgba(0,0,0,.08);
  cursor: pointer;
  transition: transform .08s ease, filter .2s ease;
}
.btn:hover { filter: brightness(1.05); }
.btn:active { transform: translateY(1px) scale(.99); }

.btn.outline {
  background: transparent;
  color: var(--accent);
  border: 2px solid rgba(255, 211, 107, .8);
  box-shadow: none;
}

.wish {
  margin-top: .25rem;
  color: var(--muted);
  font-size: clamp(1rem, 1.8vw, 1.1rem);
}

.footer {
  text-align: center;
  color: rgba(255,255,255,.7);
  font-size: .85rem;
  padding-top: .5rem;
}

/* 접근성: 모션 최소화 설정 존중 */
@media (prefers-reduced-motion: reduce) {
  .lantern, .cloud { animation: none !important; }
}
</style>
