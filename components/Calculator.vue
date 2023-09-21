<template>
    <div class="calculator">
      <!-- Calculator Display -->
      <div class="display-container" v-if="mode === 0">
        <div class="expression">{{ expression }}</div>
        <div class="icon-and-display">
          <!-- Toggle button with history icon -->
          <v-icon @click="toggleMode" class="calculator-toggle">mdi-history</v-icon>
          <div class="display">{{ display }}</div>
        </div>
      </div>
  
      <!-- History Mode -->
      <div v-else>
        <div class="history">
          <!-- Two side-by-side divs for toggle and history list -->
          <div class="history-toggle">
            <!-- Toggle button with history icon -->
            <v-icon @click="toggleMode" class="history-icon">mdi-history</v-icon>
          </div>
          <div class="history-list-container">
            <!-- Display calculation history as an unordered list -->
            <ul class="history-list">
              <li v-for="(result, index) in results" :key="index">{{ result }}</li>
            </ul>
          </div>
        </div>
      </div>
  
      <!-- Calculator Buttons -->
    <div class="buttons" v-if="mode === 0">
      <button
        class="number"
        @click="appendToDisplay('1')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        1
      </button>
      <button
        class="number"
        @click="appendToDisplay('2')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        2
      </button>
      <button
        class="number"
        @click="appendToDisplay('3')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        3
      </button>
      <button
        @click="appendToExpression('+')"
        :disabled="isDisabled"
        class="operation"
        :class="{ 'disabled-operator': isDisabled }"
      >
        +
      </button>
      <button
        class="number"
        @click="appendToDisplay('4')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        4
      </button>
      <button
        class="number"
        @click="appendToDisplay('5')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        5
      </button>
      <button
        class="number"
        @click="appendToDisplay('6')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        6
      </button>
      <button
        @click="appendToExpression('-')"
        :disabled="isDisabled"
        class="operation"
        :class="{ 'disabled-operator': isDisabled }"
      >
        -
      </button>
      <button
        class="number"
        @click="appendToDisplay('7')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        7
      </button>
      <button
        class="number"
        @click="appendToDisplay('8')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        8
      </button>
      <button
        class="number"
        @click="appendToDisplay('9')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        9
      </button>
      <button
        @click="appendToExpression('*')"
        :disabled="isDisabled"
        class="operation"
        :class="{ 'disabled-operator': isDisabled }"
      >
        *
      </button>
      <button
        class="number"
        @click="appendToDisplay('0')"
        :disabled="isDisabled"
        :class="{ 'disabled-number': isDisabled }"
      >
        0
      </button>
      <button @click="clearDisplay" class="cancel">C</button>
      <button @click="calculateResult" :disabled="isDisabled" class="equals" :class="{ 'disabled-equals': isDisabled }">
        =
      </button>
      <button
        @click="appendToExpression('/')"
        :disabled="isDisabled"
        class="operation"
        :class="{ 'disabled-operator': isDisabled }"
      >
        /
      </button>
    </div>
  </div>
</template>
    
<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  name: "Calculator",
  data() {
    return {
      display: "",
      expression: "",
      lastOperator: "",
      isErrorOrInfinity: false,
      results: [],
      userLastAction: 0,
      mode: 0,
      show: false,
    };
  },
  computed: {
    isDisabled() {
      return (
        this.display == "Error" ||
        this.display == "Infinity" ||
        isNaN(this.display) ||
        this.expression.slice(0, -1) == "Error" ||
        this.expression.slice(0, -1) == "Infinity" ||
        isNaN(this.expression.slice(0, -1))
      );
    },
  },
  methods: {
    toggleMode() {
      this.mode = this.mode === 0 ? 1 : 0; // Toggle between calculator and history mode
    },
    handleKeyPress(event: KeyboardEvent) {
      if (this.isDisabled) {
        event.preventDefault();
        return;
      }
      const key = event.key;

      if (/[0-9+\-*/]/.test(key)) {
        if(/[0-9]/.test(key)){
            this.appendToDisplay(key);
        }else{
            this.appendToExpression(key);
        }
      } else if (key === "=") {
        this.calculateResult();
      } else if (key === "Escape") {
        event.preventDefault();
        this.clearDisplay();
        this.userLastAction = -1;
      } else if (key === "Backspace") {
        event.preventDefault();
        this.deleteLastCharacter();
      } else if (key === "Enter") {
        event.preventDefault();
        this.calculateResult();
      }
    },
    deleteLastCharacter() {
      this.display = this.display.slice(0, -1);
    },
    appendToDisplay(value: string) {
      if (value === "0" && this.display === "0") {
        return;
      } else if (this.display === "0" && /[1-9]/.test(value)) {
        this.display = value;
      } else {
        if (!this.isDisabled) {
            if(this.userLastAction == 2 && !/[+\-*/]/.test(this.expression)){
                this.clearDisplay()
            }
          this.display += value;
        }
      }
      this.userLastAction = 0;
    },
    appendToExpression(value: string) {
      this.userLastAction = 1;
      if (value === "=") {
        this.calculateResult();
      } else if (/[+\-*/]/.test(value)) {
        if (this.display !== "") {
          this.display += "";
          if (/[+\-*/]/.test(this.display) && this.display.slice(0, 1) != "-") {
            if (!(this.display === "-" && this.expression === "")) {
              this.display = this.display.slice(0, -1) + value;
            }
          } else {
            this.calculateResult();
            this.expression = this.display + value;
            this.display = "";
          }
        } else {
          if (
            (!/[*/]/.test(value) && !isNaN(this.expression.slice(0, -1))) ||
            this.expression.slice(0, -1)
          ) {
            this.expression = this.expression.slice(0, -1) + value;
          }
        }
        this.lastOperator = value;
      } else {
        if(this.userLastAction == 2 && !/[+\-*/]/.test(this.display) && this.display != ""){
            this.clearDisplay()
        }
        this.display += value;
      }
    },

    clearDisplay() {
      this.display = "";
      this.expression = "";
    },
    calculateResult() {
      try {
        this.expression += this.display;
        this.display = eval(this.expression);
        if (this.userLastAction <= 1 && !(this.expression == this.display)) {
          this.results.push(this.expression + " = " + this.display);
          this.userLastAction = 2;
        }

        this.expression = "";
      } catch (error) {
        this.display = "Error";
        this.expression = "";
        this.isErrorOrInfinity = true;
      }
    },
  },
  mounted() {
    window.addEventListener("keydown", this.handleKeyPress);
  },
  beforeUnmount() {
    window.removeEventListener("keydown", this.handleKeyPress);
  },
});
</script>

<style scoped>
/* Global styles */

.calculator {
  max-width: 300px;
  margin: 0 auto;
  text-align: center;
}

/* Display styles */

.display-container {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  background-color: #f0f0f0;
  padding: 10px;
  padding-bottom: 30px;
  border: 2px solid #ccc;
  border-radius: 5px;
  margin-bottom: 10px;
  height: 75px;
}

.expression {
  position: absolute;
  font-size: 18px;
}

.display {
  position: relative;
  top: 35%;
  font-size: 24px;
  text-align: right;
}

/* Buttons styles */

.buttons {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

button {
  font-size: 18px;
  padding: 10px;
  border: none;
  cursor: pointer;
}

button.number {
  background-color: #e6e6e6;
  color: #000;
}

/* Style for disabled number buttons */
.disabled-number {
  background-color: #f0f0f0;
  color: #ccc !important;
  cursor: not-allowed;
}

/* Style for disabled operator buttons */
.disabled-operator {
  background-color: #f5ad71 !important;
  cursor: not-allowed;
}

/* Style for disabled equals button */
.disabled-equals {
  background-color: #7abdf0 !important;
  cursor: not-allowed;
}

/* Styling for operation buttons */
button.operation {
  background-color: #f5923e;
  color: #fff;
}

/* Styling for equals button */
button.equals {
  background-color: #4ca1e3;
  color: #fff;
}

/* Styling for cancel button */
button.cancel {
  background-color: #e55451;
  color: #fff;
}

/* History view styles */

.history-list {
  list-style-type: none;
  padding-left: 25px;
  text-align: left;
}

.history {
  background-color: #f0f0f0;
  padding: 10px;
  border: 2px solid #ccc;
  border-radius: 5px;
  margin-bottom: 10px;
  height: 300px;
  display: flex;
  font-size: 20px;
  overflow-x: hidden;
  text-align: justify;
  min-width: 300px;
  align-items: left;
}

/* Custom style for the rotated history icon */
.rotated {
  transform: rotate(180deg);
}

/* Style for the history toggle icon */
.history-icon {
  color: #f5923e;
  cursor: pointer;
  right: 15px;
  top: 15px;
}

/* Style for the history toggle button */
.history-toggle {
  flex: 0 0 15px;
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-start;
}

/* Style for the history list container (with scroll) */
.history-list-container {
  flex: 1;
  overflow-y: auto;
}

/* Style for calculator toggle button */
.calculator-toggle {
  left: 5px;
}

.icon-and-display {
  display: flex;
  align-items: center;
  min-width: 300px;
}

.icon-and-display v-icon {
  width: 15%;
  position: relative;
  right: 100%;
  position: fixed;
  max-width: 45px;
  float: right;
}

.icon-and-display .display {
  width: 80%;
  position: relative;
  left: 7px;
  max-width: 250px;
  float: left;
  overflow: auto;
  white-space: nowrap;
}
</style>