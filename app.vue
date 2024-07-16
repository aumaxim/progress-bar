<script setup>
import { ref } from "vue";

// Define reactive variables for user inputs and dates
const baselineDate = ref(new Date()); // The baseline date
const deadline = ref(new Date()); // The deadline date
const target = ref(100); // The target value
const value = ref(5); // The initial value
const today = ref(new Date()); // Today's date
const unit = ref(""); // The unit of measurement

// Variables to store calculated values
const calculatedvalue = ref(0); // Calculated value based on user input
const calculatedBaselineDate = ref(new Date()); // Calculated baseline date
const calculatedDeadline = ref(new Date()); // Calculated deadline date
const calculatedTarget = ref(0); // Calculated target value
const calculatedToday = ref(new Date()); // Calculated today's date
const calculatedUnit = ref(""); // Calculated unit of measurement

// Function to perform calculations and update the calculated variables
const calculateValues = () => {
  calculatedvalue.value = value.value; // Set calculated value to input value
  calculatedBaselineDate.value = baselineDate.value; // Set calculated baseline date to input baseline date
  calculatedDeadline.value = deadline.value; // Set calculated deadline to input deadline date
  calculatedTarget.value = target.value; // Set calculated target to input target value
  calculatedToday.value = today.value; // Set calculated today to input today date
  calculatedUnit.value = unit.value; // Set calculated unit to input unit
};
</script>

<template>
  <div class="mt-10 ml-5">
    <!-- Input fields for user to enter values -->
    <Input v-model="baselineDate" label="Baseline Date" inputType="date" />
    <Input v-model="deadline" label="Deadline" inputType="date" />
    <Input v-model="target" label="Target" inputType="number" />
    <Input v-model="value" label="value" inputType="number" />
    <Input v-model="unit" label="unit" inputType="text" />

    <!-- Button to trigger calculation -->
    <button
      @click="calculateValues"
      class="mt-4 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
    >
      Calculate
    </button>
  </div>

  <div class="mt-10 ml-5">
    <!-- Progress bar component displaying calculated values -->
    <Progressbar
      :value="calculatedvalue"
      :target="calculatedTarget"
      :today="calculatedToday"
      :baselineDate="calculatedBaselineDate"
      :deadline="calculatedDeadline"
      :unit="calculatedUnit"
    />
  </div>
</template>
