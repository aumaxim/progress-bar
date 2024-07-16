<script setup>
import { defineProps, defineEmits } from "vue";

// Define the properties that this component will accept
const props = defineProps({
  modelValue: [String, Number], // Value bound to the input, can be a string or a number
  label: {
    type: String,
    default: "Date", // Default label for the input
  },
  inputType: {
    type: String,
    default: "date", // Default input type is "date"
  },
});

// Define the events that this component can emit
const emits = defineEmits(["update:modelValue"]);

// Function to handle input event and emit the new value
const updateValue = (event) => {
  emits("update:modelValue", event.target.value); // Emit the new value when the input changes
};
</script>

<template>
  <div>
    <!-- Label for the input field -->
    <label :for="inputType" class="block text-large font-medium text-gray-700">
      {{ label }}
    </label>

    <!-- Input field bound to modelValue with dynamic type and id -->
    <input
      :type="inputType"
      :id="inputType"
      :name="inputType"
      class="mt-1 block px-3 py-2 border border-gray-300 bg-gray-100 rounded shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
      :value="modelValue"
      @input="updateValue"
    />
  </div>
</template>
