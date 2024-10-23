<script setup lang="ts">
import { ref, defineEmits } from "vue";
import "./style.css";

// Emits to send data back to the parent component
const emit = defineEmits<{
  (event: "inputValue", value: string): void;
  (event: "peopleCount", value: string): void;
}>();

const currentInput = ref<string>("");
const displaySymbol = ref<string>("$");
const isSelecting = ref<boolean>(false);
const inputStage = ref<number>(1); // 1 for total tip, 2 for number of people

const handleNumberClick = (num: string) => {
  currentInput.value += num;
  isSelecting.value = true;
};

const backspaceInput = () => {
  currentInput.value = currentInput.value.slice(0, -1);
};

const handleConfirm = () => {
  switch (inputStage.value) {
    case 1:
      emit("inputValue", currentInput.value);
      displaySymbol.value = "#";
      currentInput.value = "";
      inputStage.value = 2;
      break;

    case 2:
      emit("peopleCount", currentInput.value);
      currentInput.value = "";
      displaySymbol.value = "$"; 
      isSelecting.value = false;
      inputStage.value = 1;
      break;

    default:
      console.error("Unexpected input stage:", inputStage.value);
  }
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
