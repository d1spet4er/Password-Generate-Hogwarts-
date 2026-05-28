<template>
  <div class="hogwarts-app">
    <!-- Звёзды -->
    <div class="stars"></div>
    
    <!-- Свечи -->
    <div class="candles">
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
      <div class="candle"></div>
    </div>

    <div class="password-card">
      <div class="header">
        <div class="crest">🦁 🦡 🦅 🐍</div>
        <h1>ARITHMANCY <span>PASSWORD VAULT</span></h1>
        <p class="tagline">"Wingardium Leviosa — protect your secrets"</p>
      </div>

      <div class="password-display">
        <div class="runes">✧ ✦ ✧</div>
        <div class="password-value">{{ generatedPassword || 'Clavem Revelio' }}</div>
        <div class="runes">✧ ✦ ✧</div>
      </div>

      <button class="copy-btn" @click="copyToClipboard" :class="{ copied: copied }">
        {{ copied ? '✓ Copied!' : '📜 Revelio Copy' }}
      </button>

      <div class="settings">
        <div class="setting">
          <label>
            <span class="setting-label">✦ Wand length (characters):</span>
            <span class="value">{{ length }}</span>
          </label>
          <input type="range" v-model.number="length" min="8" max="28" class="slider">
        </div>

        <div class="setting">
          <label class="checkbox">
            <input type="checkbox" v-model="includeUppercase">
            <span>✦ Upper-case spells (A-Z)</span>
          </label>
        </div>

        <div class="setting">
          <label class="checkbox">
            <input type="checkbox" v-model="includeLowercase">
            <span>✦ Lower-case spells (a-z)</span>
          </label>
        </div>

        <div class="setting">
          <label class="checkbox">
            <input type="checkbox" v-model="includeNumbers">
            <span>✦ Magical numbers (0-9)</span>
          </label>
        </div>

        <div class="setting">
          <label class="checkbox">
            <input type="checkbox" v-model="includeSymbols">
            <span>✦ Ancient runes (!@#$%^&*)</span>
          </label>
        </div>
      </div>

      <button class="generate-btn" @click="generatePassword">
        🪄 ALIOHOMORA — GENERATE 🪄
      </button>

      <div class="strength" v-if="generatedPassword">
        <div class="strength-bar">
          <div class="strength-fill" :class="strengthClass" :style="{ width: strengthPercent + '%' }"></div>
        </div>
        <div class="strength-text">{{ strengthText }} protection level</div>
      </div>

      <div class="footer">
        <p>✦ Hogwarts School of Witchcraft and Wizardry ✦</p>
        <p class="quote">"Do not pity the dead, pity the living" — Albus Dumbledore</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue'

const length = ref(14)
const includeUppercase = ref(true)
const includeLowercase = ref(true)
const includeNumbers = ref(true)
const includeSymbols = ref(false)
const generatedPassword = ref('')
const copied = ref(false)

const uppercase = 'ABCDEFGHJKLMNPQRSTUVWXYZ'
const lowercase = 'abcdefghijkmnopqrstuvwxyz'
const numbers = '23456789'
const symbols = '!@#$%^&*'

const strengthPercent = computed(() => {
  let score = 0
  if (length.value >= 12) score += 20
  if (length.value >= 16) score += 15
  if (includeUppercase.value) score += 20
  if (includeLowercase.value) score += 15
  if (includeNumbers.value) score += 15
  if (includeSymbols.value) score += 25
  return Math.min(score, 100)
})

const strengthText = computed(() => {
  if (strengthPercent.value < 40) return 'Protego — Weak'
  if (strengthPercent.value < 70) return 'Repello — Medium'
  return 'Fidelius — Strong'
})

const strengthClass = computed(() => {
  if (strengthPercent.value < 40) return 'weak'
  if (strengthPercent.value < 70) return 'medium'
  return 'strong'
})

const generatePassword = () => {
  let chars = ''
  if (includeUppercase.value) chars += uppercase
  if (includeLowercase.value) chars += lowercase
  if (includeNumbers.value) chars += numbers
  if (includeSymbols.value) chars += symbols

  if (chars === '') {
    generatedPassword.value = 'You must choose at least one spell!'
    return
  }

  let password = ''
  for (let i = 0; i < length.value; i++) {
    const randomIndex = Math.floor(Math.random() * chars.length)
    password += chars[randomIndex]
  }
  generatedPassword.value = password
}

const copyToClipboard = async () => {
  if (!generatedPassword.value || generatedPassword.value.includes('choose at least one')) return
  await navigator.clipboard.writeText(generatedPassword.value)
  copied.value = true
  setTimeout(() => {
    copied.value = false
  }, 2000)
}

watch([length, includeUppercase, includeLowercase, includeNumbers, includeSymbols], () => {
  generatePassword()
})

generatePassword()
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  overflow: hidden;
}

.hogwarts-app {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100vw;
  height: 100vh;
  background: radial-gradient(ellipse at 30% 40%, #1a1520 0%, #0a0812 100%);
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: 'Georgia', 'Times New Roman', 'Segoe UI', serif;
  overflow: hidden;
}

/* ========== ЗВЁЗДЫ ========== */
.stars {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
  background-image: 
    radial-gradient(2px 2px at 5% 10%, #fff, transparent),
    radial-gradient(1px 1px at 15% 30%, #f6e58d, transparent),
    radial-gradient(2px 2px at 25% 50%, #ffd700, transparent),
    radial-gradient(1px 1px at 35% 70%, #eee, transparent),
    radial-gradient(3px 3px at 45% 20%, #fff, transparent),
    radial-gradient(2px 2px at 55% 80%, #f6e58d, transparent),
    radial-gradient(1px 1px at 65% 40%, #ffd700, transparent),
    radial-gradient(2px 2px at 75% 90%, #eee, transparent),
    radial-gradient(3px 3px at 85% 15%, #fff, transparent),
    radial-gradient(1px 1px at 95% 60%, #f6e58d, transparent),
    radial-gradient(2px 2px at 10% 85%, #ffd700, transparent),
    radial-gradient(1px 1px at 20% 5%, #eee, transparent),
    radial-gradient(2px 2px at 30% 45%, #fff, transparent),
    radial-gradient(3px 3px at 40% 95%, #f6e58d, transparent),
    radial-gradient(1px 1px at 50% 15%, #ffd700, transparent),
    radial-gradient(2px 2px at 60% 55%, #eee, transparent),
    radial-gradient(3px 3px at 70% 35%, #fff, transparent),
    radial-gradient(1px 1px at 80% 75%, #f6e58d, transparent),
    radial-gradient(2px 2px at 90% 25%, #ffd700, transparent),
    radial-gradient(1px 1px at 8% 65%, #eee, transparent),
    radial-gradient(2px 2px at 18% 95%, #fff, transparent),
    radial-gradient(3px 3px at 28% 8%, #f6e58d, transparent),
    radial-gradient(1px 1px at 38% 38%, #ffd700, transparent),
    radial-gradient(2px 2px at 48% 88%, #eee, transparent),
    radial-gradient(3px 3px at 58% 12%, #fff, transparent),
    radial-gradient(1px 1px at 68% 52%, #f6e58d, transparent),
    radial-gradient(2px 2px at 78% 32%, #ffd700, transparent),
    radial-gradient(3px 3px at 88% 72%, #eee, transparent),
    radial-gradient(1px 1px at 98% 18%, #fff, transparent);
  background-repeat: no-repeat;
  background-size: 100% 100%;
  opacity: 0.8;
  animation: starTwinkle 5s ease-in-out infinite alternate;
}

@keyframes starTwinkle {
  0% { opacity: 0.4; }
  100% { opacity: 1; }
}

/* ========== СВЕЧИ ========== */
.candles {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.candle {
  position: absolute;
  bottom: -80px;
  width: 20px;
  height: 60px;
  background: linear-gradient(180deg, #f5e6c8 0%, #d4a574 50%, #b8926a 100%);
  border-radius: 3px 3px 2px 2px;
  animation: floatCandle 8s linear infinite;
}

.candle:nth-child(1) { left: 5%; animation-delay: 0s; animation-duration: 10s; }
.candle:nth-child(2) { left: 12%; animation-delay: 2s; animation-duration: 12s; transform: scale(0.8); }
.candle:nth-child(3) { left: 20%; animation-delay: 4s; animation-duration: 9s; }
.candle:nth-child(4) { left: 28%; animation-delay: 1s; animation-duration: 11s; transform: scale(1.1); }
.candle:nth-child(5) { left: 35%; animation-delay: 3s; animation-duration: 10s; }
.candle:nth-child(6) { left: 42%; animation-delay: 5s; animation-duration: 13s; transform: scale(0.7); }
.candle:nth-child(7) { left: 50%; animation-delay: 0.5s; animation-duration: 9s; }
.candle:nth-child(8) { left: 58%; animation-delay: 2.5s; animation-duration: 11s; transform: scale(1.2); }
.candle:nth-child(9) { left: 65%; animation-delay: 4.5s; animation-duration: 10s; }
.candle:nth-child(10) { left: 72%; animation-delay: 1.5s; animation-duration: 12s; transform: scale(0.9); }
.candle:nth-child(11) { left: 78%; animation-delay: 3.5s; animation-duration: 8s; }
.candle:nth-child(12) { left: 85%; animation-delay: 5.5s; animation-duration: 11s; transform: scale(1); }
.candle:nth-child(13) { left: 92%; animation-delay: 0.8s; animation-duration: 10s; }
.candle:nth-child(14) { left: 48%; animation-delay: 6s; animation-duration: 14s; transform: scale(0.6); }
.candle:nth-child(15) { left: 33%; animation-delay: 7s; animation-duration: 9s; }

.candle::before {
  content: '';
  position: absolute;
  top: -7px;
  left: 50%;
  transform: translateX(-50%);
  width: 2px;
  height: 8px;
  background: #2a2a2a;
  border-radius: 1px;
}

.candle::after {
  content: '';
  position: absolute;
  top: -18px;
  left: 50%;
  transform: translateX(-50%);
  width: 14px;
  height: 22px;
  background: radial-gradient(ellipse at 50% 80%, #ffaa33 0%, #ff6600 50%, transparent 70%);
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
  animation: flicker 0.3s ease-in-out infinite alternate;
  filter: blur(1.5px);
}

@keyframes flicker {
  0% { transform: translateX(-50%) scale(1); opacity: 0.8; }
  100% { transform: translateX(-50%) scale(1.1); opacity: 1; }
}

@keyframes floatCandle {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0;
  }
  10% {
    opacity: 0.8;
  }
  90% {
    opacity: 0.8;
  }
  100% {
    transform: translateY(-100vh) rotate(5deg);
    opacity: 0;
  }
}

/* Карточка (остальное без изменений) */
.password-card {
  position: relative;
  z-index: 2;
  max-width: 580px;
  width: 90%;
  background: rgba(26, 22, 32, 0.92);
  backdrop-filter: blur(12px);
  border-radius: 48px;
  padding: 32px 36px;
  border: 1px solid rgba(212, 175, 55, 0.4);
  box-shadow: 0 30px 50px rgba(0, 0, 0, 0.5), inset 0 0 20px rgba(212, 175, 55, 0.05);
}

.header {
  text-align: center;
  margin-bottom: 28px;
}

.crest {
  font-size: 32px;
  margin-bottom: 12px;
  letter-spacing: 12px;
}

h1 {
  font-size: 26px;
  letter-spacing: 3px;
  font-weight: 500;
  color: #ecd672;
  text-shadow: 0 0 5px #b8860b;
}

h1 span {
  font-size: 14px;
  font-weight: 300;
  color: #ba9e5a;
  letter-spacing: 2px;
}

.tagline {
  font-size: 11px;
  margin-top: 10px;
  font-style: italic;
  color: #b9a77a;
}

.password-display {
  background: rgba(10, 8, 18, 0.8);
  border-radius: 28px;
  padding: 20px;
  margin-bottom: 18px;
  text-align: center;
  border: 1px solid rgba(212, 175, 55, 0.3);
}

.runes {
  font-size: 12px;
  letter-spacing: 6px;
  color: #6b5a3e;
  margin-bottom: 8px;
}

.password-value {
  font-size: 20px;
  font-family: monospace;
  font-weight: bold;
  color: #ecd672;
  word-break: break-all;
  letter-spacing: 1px;
}

.copy-btn {
  width: 100%;
  background: rgba(212, 175, 55, 0.15);
  border: 1px solid rgba(212, 175, 55, 0.5);
  border-radius: 40px;
  padding: 12px;
  font-size: 14px;
  font-weight: 500;
  font-family: inherit;
  color: #ecd672;
  cursor: pointer;
  transition: all 0.2s;
  margin-bottom: 25px;
}

.copy-btn:hover {
  background: rgba(212, 175, 55, 0.3);
  transform: translateY(-2px);
}

.copy-btn.copied {
  background: #2a5a3a;
  border-color: #ecd672;
}

.settings {
  background: rgba(10, 8, 18, 0.6);
  border-radius: 28px;
  padding: 20px;
  margin-bottom: 25px;
}

.setting {
  margin-bottom: 16px;
}

.setting:last-child {
  margin-bottom: 0;
}

.setting label {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 13px;
  color: #baa878;
  cursor: pointer;
}

.setting-label {
  letter-spacing: 0.5px;
}

.value {
  background: rgba(212, 175, 55, 0.2);
  padding: 2px 10px;
  border-radius: 20px;
  font-size: 12px;
  color: #ecd672;
}

.checkbox {
  display: flex;
  align-items: center;
  gap: 10px;
  cursor: pointer;
}

.checkbox input {
  width: 16px;
  height: 16px;
  cursor: pointer;
  accent-color: #d4af37;
}

.slider {
  width: 100%;
  height: 4px;
  -webkit-appearance: none;
  background: rgba(212, 175, 55, 0.3);
  border-radius: 4px;
  outline: none;
  margin-top: 12px;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  width: 16px;
  height: 16px;
  background: #d4af37;
  border-radius: 50%;
  cursor: pointer;
  border: none;
}

.generate-btn {
  width: 100%;
  background: linear-gradient(135deg, #d4af37 0%, #b8860b 100%);
  border: none;
  border-radius: 40px;
  padding: 14px;
  font-size: 15px;
  font-weight: bold;
  font-family: inherit;
  color: #1a1520;
  cursor: pointer;
  transition: all 0.2s;
  margin-bottom: 20px;
  letter-spacing: 1px;
}

.generate-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(212, 175, 55, 0.3);
}

.strength {
  margin-bottom: 20px;
}

.strength-bar {
  height: 6px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 10px;
}

.strength-fill {
  height: 100%;
  border-radius: 4px;
  transition: width 0.3s ease;
}

.strength-fill.weak {
  background: #8b0000;
}

.strength-fill.medium {
  background: #d4af37;
}

.strength-fill.strong {
  background: #2a5a3a;
}

.strength-text {
  text-align: center;
  font-size: 11px;
  color: #baa878;
  letter-spacing: 1px;
}

.footer {
  text-align: center;
  padding-top: 18px;
  border-top: 1px solid rgba(212, 175, 55, 0.2);
}

.footer p {
  font-size: 10px;
  color: #6b5a3e;
  letter-spacing: 1px;
}

.quote {
  font-style: italic;
  margin-top: 8px;
  font-size: 9px;
}

@media (max-width: 550px) {
  .password-card {
    padding: 24px;
  }
  h1 {
    font-size: 20px;
  }
  .password-value {
    font-size: 16px;
  }
  .generate-btn {
    font-size: 12px;
    padding: 12px;
  }
}
</style>