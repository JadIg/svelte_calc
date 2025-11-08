<script>
  import Keypad from './Keypad.svelte';

  export let addToEquation;
  export let clear;
  export let calculate;
</script>

<!-- Keypad -->
    <div class="grid grid-cols-4 gap-0 text-6xl">
      <Keypad expand={3} type="clear" clicked={clear}>C</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('/')}>÷</Keypad>
      
      <Keypad clicked={() => addToEquation('7')}>7</Keypad>
      <Keypad clicked={() => addToEquation('8')}>8</Keypad>
      <Keypad clicked={() => addToEquation('9')}>9</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('*')}>×</Keypad>
      
      <Keypad clicked={() => addToEquation('4')}>4</Keypad>
      <Keypad clicked={() => addToEquation('5')}>5</Keypad>
      <Keypad clicked={() => addToEquation('6')}>6</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('-')}>-</Keypad>
      
      <Keypad clicked={() => addToEquation('1')}>1</Keypad>
      <Keypad clicked={() => addToEquation('2')}>2</Keypad>
      <Keypad clicked={() => addToEquation('3')}>3</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('+')}>+</Keypad>
      
      <Keypad expand={3} clicked={() => addToEquation('0')}>0</Keypad>
      <Keypad type="equal" clicked={calculate}>=</Keypad>
    </div>