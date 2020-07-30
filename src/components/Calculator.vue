<template>
  <div class="calculator">
    <div class="display">
      <div>
        {{ calcCurrent }}
      </div>
      <div>
        {{ currentString == "" ? "..." : currentString }}
      </div>
    </div>
    <div @click="clear" class="btn">C</div>
    <div @click="sign" class="btn">+/-</div>
    <div @click="percent" class="btn">%</div>
    <div @click="squareRoot" class="btn">√</div>
    <div @click="setOperator('divide')" class="btn operator">÷</div>
    <div @click="append('7')" class="btn">7</div>
    <div @click="append('8')" class="btn">8</div>
    <div @click="append('9')" class="btn">9</div>
    <div @click="setOperator('multiply')" class="btn operator">x</div>
    <div @click="append('4')" class="btn">4</div>
    <div @click="append('5')" class="btn">5</div>
    <div @click="append('6')" class="btn">6</div>
    <div @click="setOperator('subtract')" class="btn operator">-</div>
    <div @click="append('1')" class="btn">1</div>
    <div @click="append('2')" class="btn">2</div>
    <div @click="append('3')" class="btn">3</div>
    <div @click="setOperator('add')" class="btn operator">+</div>
    <div @click="append('0')" class="btn zero">0</div>
    <div @click="dot" class="btn">.</div>
    <div @click="pi" class="btn">π</div>
    <div @click="equal" class="btn operator equal">=</div>
  </div>
</template>

<script>
import Calculator from "./Calculator";

const calc = new Calculator();

export default {
  data() {
    return {
      calcCurrent: 0,
      currentString: "",
      operator: null,
      operatorClicked: false,
      rhsOperand: null,
    };
  },
  mounted() {
    if (localStorage.calcCurrent) {
      this.calcCurrent = localStorage.calcCurrent;
    }
  },
  watch: {
    calcCurrent(newCalcCurrent) {
      localStorage.calcCurrent = newCalcCurrent;
    },
  },
  methods: {
    clear() {
      this.calcCurrent = calc.clear();
      this.currentString = "";
      this.operator = null;
      this.rhsOperand = null;
      this.operatorClicked = false;
    },
    sign() {
      // set current state of calculator to the currentString if one has been entered
      if (this.currentString) {
        calc.setCurrent(parseFloat(this.currentString || 0));
      }
      this.calcCurrent = calc.sign().toString(10);
      this.currentString = "";
    },
    percent() {
      // set current state of calculator to the currentString if one has been entered
      if (this.currentString) {
        calc.setCurrent(parseFloat(this.currentString || 0));
      }
      this.calcCurrent = calc.percent().toString(10);
      this.currentString = "";
    },
    squareRoot() {
      if (this.currentString) {
        calc.setCurrent(parseFloat(this.currentString || 0));
      }
      this.calcCurrent = calc.squareRoot().toString(10);
      this.currentString = "";
    },
    pi() {
      this.currentString = Math.PI.toFixed(2);
    },
    append(number) {
      this.currentString =
        this.currentString.length <= 8
          ? `${this.currentString}${number}`.replace(/^0/, "")
          : (this.currentString.disabled = true);
    },
    dot() {
      if (this.currentString.indexOf(".") === -1) {
        this.append(".");
      }
    },
    setOperator(operator) {
      if (this.currentString) {
        calc.setCurrent(parseFloat(this.currentString || 0));
      }
      this.currentString = "";
      this.operatorClicked = true;
      this.operator = operator;
    },
    equal() {
      // set rhsOperand only if an operator was clicked and a new value was entered
      if (this.operatorClicked && this.currentString) {
        this.rhsOperand = parseFloat(this.currentString || 0);
      }
      switch (this.operator) {
        case "add":
          calc.add(this.rhsOperand);
          break;
        case "divide":
          calc.divide(this.rhsOperand);
          break;
        case "multiply":
          calc.multiply(this.rhsOperand);
          break;
        case "subtract":
          calc.subtract(this.rhsOperand);
          break;
        default:
          break;
      }
      this.calcCurrent = calc.exec();
      this.currentString = "";
      this.operatorClicked = false;
    },
  },
};
</script>

<style scoped>
.calculator {
  margin: 0 auto;
  width: 400px;
  font-size: 40px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(50px, auto);
}
.display {
  text-align: right;
  padding: 0 10px 0 0;
  grid-column: 1 / 5;
  background-color: #333;
  color: white;
}
.equal {
  grid-column: 1 / 5;
}
.btn {
  background-color: #f2f2f2;
  user-select: none;
  cursor: pointer;
}

.btn:hover {
  border: none;
  margin: 1px;
  box-shadow: 0 10px 20px rgba(0,0,0,0.19), 0 6px 6px rgba(0,0,0,0.23);
  color: #903f37;
}
.operator {
  background-color: orange;
  color: white;
}
</style>
