<script setup lang="ts">
import "./App.css";
import NumPad from "./components/NumPad/NumPad.vue";
import { ref, computed } from "vue";

// Reactive state to hold the input values from NumPad
const valueFromMoneyNumpad = ref<string>("");
const numberOfPeople = ref<string>("");

// Function to handle the emitted input value (total tip)
const handleInputValue = (value: string) => {
  valueFromMoneyNumpad.value = value; // Store the total tip value
};

// Function to handle the emitted people count value
const handlePeopleCount = (value: string) => {
  numberOfPeople.value = value; // Store the number of people
};

// Computed property to calculate the final count per person
const finalCount = computed(() => {
  // Convert the input strings to numbers for calculation
  const totalTip = parseFloat(valueFromMoneyNumpad.value);
  const peopleCount = parseInt(numberOfPeople.value, 10);

  // Calculate and return the result formatted as a currency string
  if (peopleCount > 0) {
    const count = totalTip / peopleCount;
    return `$${count.toFixed(2)}`; // Format to 2 decimal places
  } else {
    return "$0.00"; // Return $0.00 if no people count is specified
  }
});
</script>

<template>
  <main>
    <div class="mainTop">
      <div class="left">
        <div class="leftTop">
          <div class="tipContainer">
            <p class="tipTitle">Total de propinas</p>
            <div class="tipValueContainer">
              <span class="tipValue">{{ valueFromMoneyNumpad }}</span>
            </div>
          </div>
        </div>
        <div class="leftMedium">
          <p class="peopleTitle">
            ¿Entre cúantos quieres dividir las Propinas?
          </p>
          <div class="peopleAmountContainer">
            <div class="amoutContainer">{{ numberOfPeople }}</div>
            <div class="finalCountContainer">
              <span class="finalCount">{{ finalCount }}</span>
              <span class="perPerson">x persona</span>
            </div>
          </div>
        </div>
        <div class="leftBottom">
          <div class="paymentTitle">
            <div class="cardImgContainer">
              <img src="./assets/cardsIcon.png" alt="" class="cardImg" />
            </div>
            <div class="paymentText">Elige el Método de Pago</div>
          </div>
          <div></div>
        </div>
      </div>
      <div class="middle">
        <NumPad
          @inputValue="handleInputValue"
          @peopleCount="handlePeopleCount"
        />
      </div>
      <div class="right">aaa</div>
    </div>
    <div class="mainBottom"></div>
  </main>
</template>
