<script>
  import Keypad from './Keypad.svelte';

  export let addToEquation;
  export let clear;
  export let calculate;
  export let deleteLast;
</script>

<!-- Keypad -->
    <div class="grid grid-cols-5 gap-0 text-6xl">
      <Keypad expand={3} type="clear" clicked={clear}>C</Keypad>
      <Keypad clicked={() => deleteLast()}>DEL</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('/')}>÷</Keypad>
      
      <Keypad clicked={() => addToEquation('(')}>(</Keypad>
      <Keypad clicked={() => addToEquation('7')}>7</Keypad>
      <Keypad clicked={() => addToEquation('8')}>8</Keypad>
      <Keypad clicked={() => addToEquation('9')}>9</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('*')}>×</Keypad>

      <Keypad clicked={() => addToEquation(')')}>)</Keypad>
      <Keypad clicked={() => addToEquation('4')}>4</Keypad>
      <Keypad clicked={() => addToEquation('5')}>5</Keypad>
      <Keypad clicked={() => addToEquation('6')}>6</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('-')}>-</Keypad>

      <Keypad clicked={() => addToEquation('**2')}>x²</Keypad>
      <Keypad clicked={() => addToEquation('1')}>1</Keypad>
      <Keypad clicked={() => addToEquation('2')}>2</Keypad>
      <Keypad clicked={() => addToEquation('3')}>3</Keypad>
      <Keypad type="operator" clicked={() => addToEquation('+')}>+</Keypad>

      <Keypad clicked={() => addToEquation('**0.5')}>√x</Keypad>
      <Keypad clicked={() => addToEquation('%')}>%</Keypad>
      <Keypad clicked={() => addToEquation('.')}>.</Keypad>
      <Keypad expand={1} clicked={() => addToEquation('0')}>0</Keypad>
      <Keypad type="equal" clicked={calculate}>=</Keypad>
    </div>