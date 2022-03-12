<template>           
    <div class="calculator">
        <div class="result">
            <div class="result-calculator">
                <div colspan="4">
                    <input type="text" v-model="input" disabled>
                </div>
                <div>
                    <input type="text" v-model="total" disabled>
                </div>
            </div>

            <table cellspacing="10">
                <tr>
                    <td @click="allClear" class="button dark">C</td>    
                    <td class="button dark" @click="invert">+/-</td>
                    <td class="button dark" @click="percent">%</td> 
                    <td @click="inputOperator('/')" class="button orange">/</td>                  
                </tr>
                <tr>
                    <td @click="inputNum('7')" class="button grey">7</td>
                    <td @click="inputNum('8')" class="button grey">8</td>
                    <td @click="inputNum('9')" class="button grey">9</td>
                    <td @click="inputOperator('*')" class="button orange">*</td>
                </tr>
                <tr>
                    <td @click="inputNum('4')" class="button grey">4</td>
                    <td @click="inputNum('5')" class="button grey">5</td>
                    <td @click="inputNum('6')" class="button grey">6</td>
                    <td @click="inputOperator('-')" class="button orange">-</td>
                </tr>
                <tr>
                    <td @click="inputNum('3')" class="button grey">3</td>
                    <td @click="inputNum('2')" class="button grey">2</td>
                    <td @click="inputNum('1')" class="button grey">1</td>
                    <td @click="inputOperator('+')" class="button orange">+</td>
                </tr>
                <tr>
                    <td @click="inputNum('0')" class="button grey" colspan="2">0</td>
                    <td class="button grey" @click="addPoint">.</td>
                    <td @click="calculation" class="button orange">=</td>
                </tr>
        </table>
        </div>
    </div>  
</template>

<script>
export default {
  name: 'calculator',
  data(){
    return{
      input: "",
      total: "",
    }
  },
  methods: {
    invert() {
        this.input *= -1;
    },

    percent() {
        this.input /= 100;
    },

    addPoint() {
        if(!this.input.includes('.'))
          this.input += '.';
    },

    inputNum(number){
      if(this.input === "" && number === '0' )return;
      this.input += number;
    },

    inputOperator(operator) {
      if(this.input === "" && operator != "-")return;
      else if(this.input[this.input.length - 1] === " ")this.input = this.input.substring(0, this.input.length - 2) + operator + " ";
      else this.input += " " + operator + " ";
    },

    calculation() {
      if(this.input === "")return;
      this.total = this.expressionParser(this.input);
      this.input = "";
    },

    expressionParser(expression) {
      let numbers = [];
      let ops = [];
      expression = expression.replace(" ", "");
      if(isNaN(expression[expression.length - 2]) && expression.length != 1){
        expression += "0";
      }
      for(let i = 0;i < expression.length;i++){
        if(isNaN(expression[i])){
          let currOperator = expression[i];
          while(ops.length > 0 && this.getPriority(currOperator) <= this.getPriority(ops[ops.length - 1])){
            this.process(numbers, ops[ops.length - 1]);
            ops.pop();
          }
          ops.push(currOperator);
        }
        else{
          let number = "";
          while(i < expression.length && !isNaN(expression[i])){
            number += expression[i];
            i++;
          }
          i--;
          numbers.push(number);
        }
      }
        while(ops.length > 0){
          this.process(numbers, ops[ops.length - 1]);
          ops.pop();
        }
        return numbers[0];
    },

    process(stack, operator){
      
      let right = parseInt(stack.pop());
      let left = parseInt(stack.pop());
      let value = 0;
      switch(operator) {
        case '+': 
            value = left + right;
        break;
        case '-':
            value = left - right;
        break;
        case '*': 
            value = left * right;
        break;
        case '/': 
            value = left / right;
        break;
      }
      stack.push(value);
    },

    getPriority(operator){
      if(operator === "+" || operator === "-")return 1;
      else if(operator === "*" || operator === "/")return 2;
    },

    allClear(){
      this.input = "";
      this.total = "";
    },
  }
}
</script>
