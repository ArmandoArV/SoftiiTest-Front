<script setup lang="ts">
import { ref } from "vue";
import "./style.css";

// Reactive state to manage input and symbol status
const currentInput = ref<string>("");
const displaySymbol = ref<string>("$"); // Initially, the money symbol
const isSelecting = ref<boolean>(false); // Track if a button is selected

// Function to handle number button clicks
const handleNumberClick = (num: string) => {
  currentInput.value += num;
  isSelecting.value = true; // Set selecting state to true
};

// Function to remove the last character (backspace)
const backspaceInput = () => {
  currentInput.value = currentInput.value.slice(0, -1);
};

// Function to handle the confirm button
const handleConfirm = () => {
  displaySymbol.value = "#"; // Change to '#' after confirmation
  isSelecting.value = false; // Reset selecting state after confirmation
};
</script>

<template>
  <div :class="['containerCalculator', { selecting: isSelecting }]">
    <div class="display">
      <span>{{ displaySymbol }} {{ currentInput }}</span>
      <button class="clear-button" @click="backspaceInput">
        <img src="../../assets/backspace.png" class="imgFit" />
      </button>
    </div>

    <!-- Numpad -->
    <div class="numpad">
      <button
        class="element-button"
        v-for="num in ['1', '2', '3', '4', '5', '6', '7', '8', '9']"
        :key="num"
        @click="handleNumberClick(num)"
      >
        {{ num }}
      </button>
      <button class="element-button" @click="handleNumberClick('00')">
        00
      </button>
      <button class="element-button" @click="handleNumberClick('0')">0</button>
      <button class="confirm-button" @click="handleConfirm">
        <img src="../../assets/tick.png" class="imgFit" />
      </button>
    </div>
  </div>
</template>
