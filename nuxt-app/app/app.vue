<template>
  <div class="calculator">
    <div class="display">
      <div class="expression">{{ expression }}</div>
      <div class="result">{{ current || '0' }}</div>
    </div>adfadfasfasdgiajdghdsafh
    <div @click="append('(')" class="btn sci">(</div>
    <div @click="append(')')" class="btn sci">)</div>
    <div @click="clear" class="btn">C</div>
    <div @click="sign" class="btn">+/-</div>
    <div @click="chooseOperator((a, b) => a % b, '%')" class="btn">%</div>
    <div @click="chooseOperator((a, b) => a / b, '÷')" class="btn operator">÷</div>
    <div @click="scientific('tan')" class="btn sci">tan</div>
    <div @click="scientific('ln')" class="btn sci">ln</div>
    <div @click="append('7')" class="btn">7</div>
    <div @click="append('8')" class="btn">8</div>
    <div @click="append('9')" class="btn">9</div>
    <div @click="chooseOperator((a, b) => a * b, '×')" class="btn operator">×</div>
    <div @click="scientific('sqr')" class="btn sci">x²</div>
    <div @click="scientific('sqrt')" class="btn sci">√</div>
    <div @click="append('4')" class="btn">4</div>
    <div @click="append('5')" class="btn">5</div>
    <div @click="append('6')" class="btn">6</div>
    <div @click="chooseOperator((a, b) => a - b, '−')" class="btn operator">-</div>
    <div @click="scientific('pi')" class="btn sci">π</div>
    <div @click="scientific('e')" class="btn sci">e</div>
    <div @click="append('1')" class="btn">1</div>
    <div @click="append('2')" class="btn">2</div>
    <div @click="append('3')" class="btn">3</div>
    <div @click="chooseOperator((a, b) => a + b, '+')" class="btn operator">+</div>
    <div @click="scientific('sin')" class="btn sci">sin</div>
    <div @click="scientific('cos')" class="btn sci">cos</div>
    <div @click="append('0')" class="btn zero">0</div>
    <div @click="dot" class="btn">.</div>
    <div @click="equal" class="btn operator">=</div>
  </div>
</template>
<script>
asdfuj
export default {
  data() {
    return {
      previous: null,
      current: '',
      operator: null,
      operatorClicked: false,
      expression: '',
      operatorSymbol: '',
      lastOperator: null,
      lastOperatorSymbol: '',
      lastNumber: null,
      justCalculated: false,
      exprMode: false,
      exprBase: '',
      parenDepth: 0
    }
  },
  methods: {
    clear() {
      this.previous = null
      this.current = ''
      this.operator = null
      this.operatorClicked = false
      this.expression = ''
      this.operatorSymbol = ''
      this.lastOperator = null
      this.lastOperatorSymbol = ''
      this.lastNumber = null
      this.justCalculated = false
      this.resetParen()
    },
    resetParen() {
      this.exprMode = false
      this.exprBase = ''
      this.parenDepth = 0
    },
    wrap(num) {
      return `${num}`.startsWith('-') ? `(${num})` : `${num}`
    },
    sign() {
      if (!this.current || this.current === 'Error') {
        return
      }
      if (this.current.charAt(0) === '-') {
        this.current = this.current.slice(1)
      } else {
        this.current = `-${this.current}`
      }
      this.updateExpression()
    },
    append(value) {
      if (this.justCalculated) {
        this.current = ''
        this.previous = null
        this.operator = null
        this.operatorClicked = false
        this.expression = ''
        this.operatorSymbol = ''
        this.lastOperator = null
        this.lastOperatorSymbol = ''
        this.lastNumber = null
        this.justCalculated = false
        this.resetParen()
      }
      if (value === '(' || value === ')') {
        this.handleParentheses(value)
        return
      }
      if (this.current === 'Error') {
        this.current = ''
      }
      if (
        this.exprMode &&
        this.current === '' &&
        this.exprBase.slice(-1) === ')'
      ) {
        this.exprBase += '×'
      }
      if (this.operatorClicked) {
        this.current = ''
        this.operatorClicked = false
      }
      if (this.current === '0') {
        this.current = value
      } else {
        this.current = `${this.current}${value}`
      }
      this.updateExpression()
    },
    handleParentheses(value) {
      if (this.current === 'Error') {
        this.clear()
      }
      if (value === '(') {
        if (!this.exprMode) {
          let base = ''
          if (this.operator && this.previous !== null) {
            base = `${this.wrap(this.previous)}${this.operatorSymbol}`
            if (!this.operatorClicked && this.current !== '') {
              base += `${this.wrap(this.current)}×`
            }
          } else if (this.current !== '') {
            base = `${this.wrap(this.current)}×`
          }
          this.exprBase = base
          this.exprMode = true
          this.previous = null
          this.operator = null
          this.operatorSymbol = ''
        } else if (this.current !== '') {
          this.exprBase += `${this.wrap(this.current)}×`
        } else if (this.exprBase.slice(-1) === ')') {
          this.exprBase += '×'
        }
        this.exprBase += '('
        this.parenDepth++
        this.current = ''
        this.operatorClicked = false
        this.expression = this.exprBase
        return
      }
      if (value === ')') {
        if (!this.exprMode || this.parenDepth === 0) {
          return
        }
        if (this.current !== '') {
          this.exprBase += this.wrap(this.current)
          this.current = ''
        } else if (/[+−×÷%(]$/.test(this.exprBase)) {
          return
        }
        this.exprBase += ')'
        this.parenDepth--
        this.expression = this.exprBase
      }
    },
    scientific(type) {
      let value;
      if (type === 'pi') {
        value = Math.PI
      }
      else if (type === 'e') {
        value = Math.E
      }
      else {
        value = parseFloat(this.current)
        if (isNaN(value)) {
          return
        }
      }
      let result;
      switch (type) {
        case 'sin':
          result = Math.sin(value)
          break
        case 'cos':
          result = Math.cos(value)
          break
        case 'tan':
          result = Math.tan(value)
          break
        case 'ln':
          if (value <= 0) {
            this.current = 'Error'
            return
          }
          result = Math.log(value)
          break
        case 'sqr':
          result = Math.pow(value, 2)
          break
        case 'sqrt':
          if (value < 0) {
            this.current = 'Error'
            return
          }
          result = Math.sqrt(value)
          break
        case 'pi':
          result = Math.PI
          break
        case 'e':
          result = Math.E
          break
        default:
          return
      }
      this.current = this.formatResult(result)
      this.justCalculated = false
      this.updateExpression()
    },
    dot() {
      if (this.current === 'Error') {
        this.current = '0'
      }
      if (
        this.exprMode &&
        this.current === '' &&
        this.exprBase.slice(-1) === ')'
      ) {
        this.exprBase += '×'
      }
      if (this.operatorClicked) {
        this.current = '0'
        this.operatorClicked = false
      }
      if (!this.current.includes('.')) {
        this.current = this.current || '0'
        this.current += '.'
      }
      this.updateExpression()
    },
    chooseOperator(operation, symbol) {
      if (this.current === 'Error') {
        return
      }
      if (this.exprMode) {
        if (this.current !== '') {
          this.exprBase += this.wrap(this.current)
          this.current = ''
        } else if (this.exprBase.slice(-1) === '(') {
          return
        } else if (/[+−×÷%]$/.test(this.exprBase)) {
          this.exprBase = this.exprBase.slice(0, -1)
        }
        this.exprBase += symbol
        this.expression = this.exprBase
        return
      }
      if (this.current === '') {
        this.current = '0'
      }
      if (this.operatorClicked) {
        this.operator = operation
        this.operatorSymbol = symbol

        this.expression = `${this.previous} ${symbol}`
        return
      }
      if (
        this.operator &&
        this.previous !== null &&
        this.current !== ''
      ) {
        this.calculate()
        if (this.current === 'Error') {
          return
        }
        this.previous = this.current
      }
      else {
        this.previous = this.current
      }
      this.operator = operation
      this.operatorSymbol = symbol
      this.operatorClicked = true
      this.justCalculated = false
      this.expression = `${this.previous} ${symbol}`
    },
    calculate() {
      const a = parseFloat(this.previous)
      const b = parseFloat(this.current)
      if (this.operator === null) {
        return
      }
      if (
        (this.operatorSymbol === '÷' ||
          this.operatorSymbol === '%') &&
        b === 0
      ) {
        this.current = 'Error'
        this.previous = null
        this.operator = null
        this.operatorSymbol = ''
        this.operatorClicked = false
        return
      }
      const result = this.operator(a, b)
      this.current = this.formatResult(result)
    },
    equal() {
      if (this.current === 'Error') {
        return
      }
      if (this.exprMode) {
        if (this.current !== '') {
          this.exprBase += this.wrap(this.current)
          this.current = ''
        }
        let exp = this.exprBase.replace(/[+−×÷%(]+$/, '')
        if (!exp) {
          return
        }
        const open = (exp.match(/\(/g) || []).length
        const close = (exp.match(/\)/g) || []).length
        exp += ')'.repeat(Math.max(0, open - close))
        this.calculateExpression(exp)
        return
      }
      if (
        !this.operator ||
        this.previous === null ||
        this.current === ''
      ) {
        return
      }
      this.lastNumber = this.current
      this.lastOperator = this.operator
      this.lastOperatorSymbol = this.operatorSymbol
      const firstNumber = this.previous
      const secondNumber = this.current
      const symbol = this.operatorSymbol
      this.calculate()
      if (this.current === 'Error') {
        return
      }
      this.expression =
        `${firstNumber} ${symbol} ${secondNumber}`
      this.operator = null
      this.operatorClicked = false
      this.justCalculated = true
    },
    formatResult(value) {
      if (!isFinite(value)) {
        return 'Error'
      }
      return `${parseFloat(value.toFixed(10))}`
    },
    calculateExpression(exp) {
      try {
        const js = exp
          .replace(/×/g, '*')
          .replace(/÷/g, '/')
          .replace(/−/g, '-')
        const result = Function(
          `"use strict"; return (${js})`
        )()

        if (!isFinite(result)) {
          this.current = 'Error'
        } else {
          this.current = this.formatResult(result)
          this.expression = `${exp} =`
          this.justCalculated = true
        }
      } catch (error) {
        this.current = 'Error'
      }
      this.resetParen()
    },
    updateExpression() {
      if (this.exprMode) {
        this.expression = this.exprBase + this.current
        return
      }
      if (this.operatorSymbol && this.previous !== null) {
        this.expression =
          `${this.previous} ${this.operatorSymbol} ${this.current}`
      } else {
        this.expression = this.current
      }
    }
  }
}
</script>
<style scoped>
  .calculator {
    font-family: Arial, Helvetica, sans-serif;
    background-color: #1c1c1c;
    padding: 20px;
    width: 550px;
    max-width: 550px;
    border-radius: 12px;
    margin: 0 auto;
    display: grid;
    grid-template-columns: repeat(6, 1fr);
    gap: 8px;
  }
  .display {
    grid-column: span 6;
    background-color: #1c1c1c;
    color: white;
    text-align: right;
    font-size: 60px;
    padding: 10px;
    overflow-x: auto;
  }
  .expression {
    font-size: 24px;
    color: #aaa;
    min-height: 30px;
  }
  .result {
    font-size: 60px;
  }
  .btn {
    background-color: #494a4d;
    color: white;
    font-size: 26px;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 70px;
    border-radius: 35px;
    cursor: pointer;
    transition: background-color 0.2s, transform 0.1s;
  }
  .btn.sci {
    background-color: #333336;
    font-size: 22px;
  }
  .btn.operator {
    background-color: #f2b463;
  }
  .btn.zero {
    grid-column: span 2;
  }
  .btn:hover {
    background-color: #616368;
  }
  .btn.sci:hover {
    background-color: #444448;
  }
  .btn.operator:hover {
    background-color: #ffc478;
  }
  .btn:active,
  .btn.sci:active {
    background-color: #222225;
    transform: scale(0.95);
  }
  .btn.operator:active {
    background-color: #d99945;
    transform: scale(0.95);
  }
</style>