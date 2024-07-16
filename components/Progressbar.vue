<script setup>
import { defineProps, computed, ref } from "vue";

// Define the properties passed to this component
const props = defineProps({
  unit: String,
  value: Number,
  target: Number,
  baselineDate: Date,
  deadline: Date,
  today: Date,
});

// Compute the progress percentage
const progress = computed(() => {
  return Math.round((props.value / props.target) * 100);
});

// Reactive variables for UI interactions
const switchState = ref(false);
const isGreenHovered = ref(false);
const isStripedHovered = ref(false);
const isGreyHovered = ref(false);
const mouseX = ref(0);
const mouseY = ref(0);

// Function to calculate baseline values
const calculateBaselineValues = () => {
  const baseline = new Date(props.baselineDate);
  const deadline = new Date(props.deadline);
  const today = new Date(props.today);

  const totalDuration = deadline - today;
  const elapsedDuration = baseline - today;
  console.log(deadline, baseline, today);

  const baselineValues = (elapsedDuration / totalDuration) * props.target;
  return baselineValues;
};

// Compute baseline progress percentage
const baselineProgress = computed(
  () => (calculateBaselineValues() / props.target) * 100
);

// Compute if the progress is special (either 0%, 100%, or matches baseline progress)
const isSpecialProgress = computed(() => {
  return (
    progress.value === 0 ||
    progress.value === 100 ||
    Math.round(baselineProgress.value) === progress.value
  );
});

// Function to update mouse position for hover effects
const updateMousePosition = (event) => {
  mouseX.value = event.clientX;
  mouseY.value = event.clientY;
};
</script>

<template>
  <div v-if="target" class="relative w-80">
    <!-- Display value or progress percentage and a toggle switch -->
    <div class="flex items-center mt-2 justify-between">
      <p class="font-bold text-xl">
        {{ switchState ? `${progress}%` : `${value}${unit}` }}
      </p>
      <div class="flex items-center">
        <label class="switch relative">
          <input type="checkbox" v-model="switchState" />
          <span class="slider round"></span>
        </label>
        <span class="ml-2">{{ switchState ? "Progress" : "Result" }}</span>
      </div>
    </div>

    <!-- Progress bar container -->
    <div
      @mouseover="isGreyHovered = true"
      @mouseleave="isGreyHovered = false"
      @mousemove="updateMousePosition"
      class="bg-gray-200 rounded-md h-3 mt-2 relative cursor-pointer"
    >
      <!-- Tooltip for grey background -->
      <p
        v-if="isGreyHovered && !isStripedHovered"
        class="fixed bg-gray-100 z-10 rounded-md text-xs mt-1"
        :style="{ left: `${mouseX}px`, top: `${mouseY + 20}px` }"
      >
        {{
          switchState
            ? `Target is 100% by ${new Date(deadline).toLocaleDateString(
                "en-GB"
              )}`
            : `Target is ${target}${unit} by ${new Date(
                deadline
              ).toLocaleDateString("en-GB")}`
        }}
      </p>
      <!-- Green progress bar -->
      <div
        @mouseover="isGreenHovered = true"
        @mouseleave="isGreenHovered = false"
        @mousemove="updateMousePosition"
        :style="{ width: progress + '%' }"
        class="bg-green-400 h-3 rounded-md absolute"
      ></div>
      <!-- Tooltip for green progress bar -->
      <p
        v-if="isGreenHovered"
        class="fixed bg-gray-100 z-10 rounded-md text-xs mt-1"
        :style="{ left: `${mouseX}px`, top: `${mouseY + 20}px` }"
      >
        {{
          switchState
            ? `${progress}% status on ${new Date(today).toLocaleDateString(
                "en-GB"
              )}`
            : `${value}${unit} status on ${new Date(today).toLocaleDateString(
                "en-GB"
              )}`
        }}
      </p>
      <!-- Striped baseline progress bar -->
      <div
        @mouseover="isStripedHovered = true"
        @mouseleave="isStripedHovered = false"
        @mousemove="updateMousePosition"
        :style="{ width: baselineProgress + '%' }"
        class="stripes h-3 rounded-md"
      ></div>
      <!-- Tooltip for striped baseline progress bar -->
      <p
        v-if="isStripedHovered"
        class="fixed bg-gray-100 z-10 rounded-md text-xs mt-1"
        :style="{ left: `${mouseX}px`, top: `${mouseY + 20}px` }"
      >
        {{
          switchState
            ? `${Math.round(baselineProgress)}% to be on track ${new Date(
                baselineDate
              ).toLocaleDateString("en-GB")}`
            : `${Math.round(
                calculateBaselineValues()
              )}${unit} to be on track ${new Date(
                baselineDate
              ).toLocaleDateString("en-GB")}`
        }}
      </p>
    </div>

    <!-- Progress bar labels and indicators -->
    <div class="progress-container cursor-pointer">
      <p
        @mouseover="isGreyHovered = true"
        @mouseleave="isGreyHovered = false"
        @mousemove="updateMousePosition"
        ref="0"
        class="text-left"
      >
        {{ switchState ? "0%" : `0${unit}` }}
      </p>
      <p
        ref="100"
        class="text-right"
        @mouseover="isGreyHovered = true"
        @mouseleave="isGreyHovered = false"
        @mousemove="updateMousePosition"
      >
        {{ switchState ? "100%" : `${target}${unit}` }}
      </p>

      <p
        @mouseover="isStripedHovered = true"
        @mouseleave="isStripedHovered = false"
        @mousemove="updateMousePosition"
        v-if="
          Math.round(baselineProgress) !== 0 &&
          Math.round(baselineProgress) !== 100
        "
        ref="nbaseline"
        :style="{ left: `calc(${baselineProgress}% - 2px)` }"
        class="absolutes nbaseline"
      >
        {{
          switchState
            ? `${Math.round(baselineProgress)}%`
            : `${Math.round(calculateBaselineValues())}${unit}`
        }}
      </p>
      <p
        @mouseover="isGreenHovered = true"
        @mouseleave="isGreenHovered = false"
        @mousemove="updateMousePosition"
        v-if="!isSpecialProgress"
        ref="nprogress"
        :style="{ left: `calc(${progress}% - 2px)` }"
        class="absolutes nprogress"
      >
        {{ switchState ? `${progress}%` : `${value}${unit}` }}
      </p>
    </div>
  </div>
  <!-- Placeholder for when target is not defined -->
  <div v-else class="">
    <div class="relative w-80">
      <div class="flex items-center mt-2 justify-between">
        <div class="bg-gray-100 rounded-md h-3 mt-2 w-20"></div>
        <v-spacer></v-spacer>
        <div class="bg-gray-100 rounded-md h-3 mt-2 w-10"></div>
      </div>
      <div class="bg-gray-100 rounded-md h-3 mt-2 relative"></div>
    </div>
  </div>
</template>

<style>
/* Switch component styles */
.switch {
  display: inline-block;
  width: 2em;
  height: 1em;
}

.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #
      ;
  transition: 0.4s;
  border-radius: 1.5em;
}

.slider:before {
  position: absolute;
  content: "";
  height: 0.75em;
  width: 0.75em;
  left: 0.15em;
  bottom: 0.15em;
  background-color: white;
  transition: 0.4s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: #4caf50;
}

input:checked + .slider:before {
  transform: translateX(1em);
}

/* Striped background style */
.stripes {
  background: repeating-linear-gradient(
    -45deg,
    #ccc,
    #ccc 5px,
    #ffffffcb 5px,
    #ffffffcb 10px
  );
}

p {
  font-size: 0.75rem;
}

/* Progress container and labels */
.progress-container {
  position: relative;
  display: flex;
  justify-content: space-between;
}

.text-left,
.text-right {
  position: relative;
}

.absolutes {
  position: absolute;
  top: 0;
  white-space: nowrap;
  transform: translateX(-50%);
}

/* Overlap handling for labels */
.nprogress,
.nbaseline {
  white-space: nowrap;
  z-index: 1;
}

.nprogress + .nprogress,
.nbaseline + .nbaseline,
.nprogress + .nbaseline,
.nbaseline + .nprogress {
  top: 20px;
}
</style>
