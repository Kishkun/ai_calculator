<template>
  <div class="calculator-app">
    <div class="calculator" :class="{ 'dark-mode': isDarkMode }">
      <div class="display">
        <div class="previous-operand">{{ previousOperand }} {{ operation }}</div>
        <div class="current-operand">{{ currentOperand || '0' }}</div>
      </div>
      
      <div class="theme-toggle" @click="toggleTheme">
        {{ isDarkMode ? '🌞' : '🌙' }}
      </div>

      <div class="keypad">
        <button @click="clear" class="span-two function">AC</button>
        <button @click="deleteNumber" class="function">DEL</button>
        <button @click="chooseOperation('÷')" class="operator">÷</button>

        <button @click="appendNumber('7')">7</button>
        <button @click="appendNumber('8')">8</button>
        <button @click="appendNumber('9')">9</button>
        <button @click="chooseOperation('×')" class="operator">×</button>

        <button @click="appendNumber('4')">4</button>
        <button @click="appendNumber('5')">5</button>
        <button @click="appendNumber('6')">6</button>
        <button @click="chooseOperation('-')" class="operator">-</button>

        <button @click="appendNumber('1')">1</button>
        <button @click="appendNumber('2')">2</button>
        <button @click="appendNumber('3')">3</button>
        <button @click="chooseOperation('+')" class="operator">+</button>

        <button @click="appendNumber('0')" class="span-two">0</button>
        <button @click="appendNumber('.')">.</button>
        <button @click="compute" class="operator">=</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { usePreferredDark } from '@vueuse/core'

const isDark = usePreferredDark()
const isDarkMode = ref(isDark.value)

const currentOperand = ref('')
const previousOperand = ref('')
const operation = ref('')

const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
}

const clear = () => {
  currentOperand.value = ''
  previousOperand.value = ''
  operation.value = ''
}

const deleteNumber = () => {
  currentOperand.value = currentOperand.value.slice(0, -1)
}

const appendNumber = (number: string) => {
  if (number === '.' && currentOperand.value.includes('.')) return
  currentOperand.value = currentOperand.value + number
}

const chooseOperation = (op: string) => {
  if (currentOperand.value === '') return
  if (previousOperand.value !== '') {
    compute()
  }
  operation.value = op
  previousOperand.value = currentOperand.value
  currentOperand.value = ''
}

const compute = () => {
  let computation
  const prev = parseFloat(previousOperand.value)
  const current = parseFloat(currentOperand.value)
  if (isNaN(prev) || isNaN(current)) return

  switch (operation.value) {
    case '+':
      computation = prev + current
      break
    case '-':
      computation = prev - current
      break
    case '×':
      computation = prev * current
      break
    case '÷':
      computation = prev / current
      break
    default:
      return
  }

  currentOperand.value = computation.toString()
  operation.value = ''
  previousOperand.value = ''
}
</script>

<style>
.calculator-app {
  min-height: 100vh;
  display: grid;
  place-items: center;
  background: var(--bg-color);
  transition: background-color 0.3s ease;
}

.calculator {
  --primary-dark: #17181a;
  --primary-light: #ffffff;
  --secondary-dark: #292d36;
  --secondary-light: #f9f9f9;
  --accent: #26feb7;
  --text-dark: #ffffff;
  --text-light: #000000;
  
  --bg-color: var(--secondary-light);
  --calculator-bg: var(--primary-light);
  --button-bg: var(--secondary-light);
  --text-color: var(--text-light);

  background: var(--calculator-bg);
  border-radius: 20px;
  width: 100%;
  max-width: 400px;
  padding: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.calculator.dark-mode {
  --bg-color: var(--primary-dark);
  --calculator-bg: var(--secondary-dark);
  --button-bg: var(--primary-dark);
  --text-color: var(--text-dark);
}

.display {
  padding: 20px;
  text-align: right;
  background: var(--calculator-bg);
  border-radius: 10px;
  margin-bottom: 20px;
  min-height: 100px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  word-wrap: break-word;
  word-break: break-all;
}

.previous-operand {
  color: rgba(var(--text-color), 0.75);
  font-size: 1.5rem;
}

.current-operand {
  color: var(--text-color);
  font-size: 2.5rem;
  font-weight: 700;
}

.theme-toggle {
  position: absolute;
  top: 20px;
  right: 20px;
  cursor: pointer;
  font-size: 1.5rem;
  user-select: none;
}

.keypad {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

button {
  padding: 20px;
  font-size: 1.5rem;
  border: none;
  outline: none;
  cursor: pointer;
  background: var(--button-bg);
  color: var(--text-color);
  border-radius: 10px;
  transition: all 0.2s ease;
}

button:hover {
  opacity: 0.9;
}

button:active {
  transform: scale(0.95);
}

.span-two {
  grid-column: span 2;
}

.operator {
  background: var(--accent);
  color: var(--calculator-bg);
}

.function {
  background: #ff5252;
  color: white;
}

@media (max-width: 500px) {
  .calculator {
    width: 100%;
    height: 100vh;
    border-radius: 0;
  }
}
</style>