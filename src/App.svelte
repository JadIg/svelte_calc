<script>
  import { fly } from 'svelte/transition';
  import BasicCalculator from './BasicCalculator.svelte';
  import ScientificCalculator from './ScientificCalculator.svelte';
  import './app.css';
  
  let numberInput = $state("");
  let total = $state(0);
  let history = $state([]);
  let showHistory = $state(false);
  let lastOperation = $state("");
  let showExtended = $state(false);
  let openParentheses = $state(0);
  let closeParentheses = $state(0);
  
  function isOperator(char) {
    return ['+', '-', '*', '/'].includes(char);
  }
  
  function applyPercent() {
    let lastOpIndex = -1;
    let operator = '';
    for (let i = numberInput.length - 1; i >= 0; i--) {
      if (isOperator(numberInput[i])) {
        lastOpIndex = i;
        operator = numberInput[i];
        break;
      }
    }
    
    if (lastOpIndex === -1) {
      numberInput = (parseFloat(numberInput) / 100).toString();
    } else {
      const leftNum = parseFloat(numberInput.substring(0, lastOpIndex));
      const rightNum = parseFloat(numberInput.substring(lastOpIndex + 1));
      const percentValue = (leftNum * rightNum) / 100;
      
      numberInput = numberInput.substring(0, lastOpIndex + 1) + percentValue;
    }
  }
  
  function addToEquation(value) {
    const lastChar = numberInput.slice(-1);
    
    // Handle percent
    if (value === '%') {
      applyPercent();
      return;
    }
    
    // Handle (
    if (value === '(') {
      if (!isNaN(Number(lastChar)) || lastChar === ')') {
        numberInput += '*';
      }
      numberInput += value;
      openParentheses += 1;
      return;
    }
    
    // Handle closing parenthesis
    if (value === ')') {
      if (openParentheses > closeParentheses && !isOperator(lastChar) && lastChar !== '(') {
        numberInput += value;
        closeParentheses += 1;
      }
      return;
    }
    
    if (isOperator(value)) {
      if (isOperator(lastChar) || numberInput === "") {
        return;
      }
    }
    numberInput += value;
  }
  
  const clear = () => {
    total = 0;
    numberInput = "";
    history = [];
    lastOperation = "";
    openParentheses = 0;
    closeParentheses = 0;
  }
  
  function calculate() {
    // If no operand and there's hx: repeat last operation
    if (numberInput !== "" && !numberInput.includes('+') && !numberInput.includes('-') && !numberInput.includes('*') && !numberInput.includes('/') && lastOperation !== "") {
      numberInput = numberInput + lastOperation;
    }

    if (numberInput.slice(-1) === '(') {
      return; 
    }
    
    // history entry
    history.push(numberInput.split(/([+\-*/])/).join(" ") + " = " + result());
  
    // save last operation for next time
    let lastOpIndex = -1;
    for (let i = numberInput.length - 1; i >= 0; i--) {
      if (isOperator(numberInput[i])) {
        lastOpIndex = i;
        break;
      }
    }
    lastOperation = lastOpIndex !== -1 ? numberInput.slice(lastOpIndex) : "";
    
    // calculate result
    if (numberInput !== "") {
      numberInput = result().toString();
      total = Number(numberInput);
      openParentheses = 0;
      closeParentheses = 0;
    }
  }
  
  let result = () => {
    try {
      const lastChar = numberInput.slice(-1);
      let calculation = 0;
      if (lastChar === ')') {
        calculation = eval(numberInput);
      } else if (!isNaN(Number(lastChar))) {
        calculation = eval(numberInput);
      } else if (isOperator(lastChar)) {
        calculation = eval(numberInput.slice(0, -1));
      } else {
        calculation = eval(numberInput);
      }
      
      // Limit to 4 numbers after decimal
      return Math.round(calculation * 10000) / 10000;
    } catch (e) {
      return 0; 
    }
  }

  function deleteLast() {
    if (numberInput.slice(-1) === ')') {
      closeParentheses -= 1;
    } else if (numberInput.slice(-1) === '(') {
      openParentheses -= 1;
    }
    numberInput = numberInput.slice(0, -1);
  }
  
  let keyboardDiv;
  
  // Keyboard interactivity 
  function handleKeyboard(e) {
    const key = e.key;
    
    if (key >= '0' && key <= '9') {
      addToEquation(key);
    }
    else if (key === '+' || key === '-' || key === '*' || key === '/') {
      addToEquation(key);
    }
    else if (key === '.') {
      addToEquation('.');
    }
    else if (key === '(') {
      addToEquation('(');
    }
    else if (key === ')') {
      addToEquation(')');
    }
    else if (key === '%') {
      addToEquation('%');
      e.preventDefault();
    }
    else if (key === 'Enter' || key === '=') {
      calculate();
      e.preventDefault();
    }
    else if (key === 'Escape' || key.toLowerCase() === 'c') {
      clear();
      e.preventDefault();
    }
    else if (key === 'Backspace') {
      deleteLast();
      e.preventDefault();
    }
  }
  
  $effect(() => {
    if (keyboardDiv) {
      keyboardDiv.focus();
    }
  });
  
  $effect(() => {
    if (
      numberInput !== "" &&
      !isNaN(Number(numberInput.slice(-1))) &&
      numberInput != result().toString()
    ) {
      total = result();
    }
  });

</script>

<main class="flex flex-col items-center justify-center min-h-screen bg-gray-100 font-light">
  <!-- svelte-ignore a11y_no_noninteractive_tabindex -->
  <!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
  <div bind:this={keyboardDiv} onkeydown={handleKeyboard} tabindex="-1" role="application" class="outline-none">
  <h1 class="text-4xl font-bold mb-8 text-zinc-800">Svelte Calculator</h1>
  <div in:fly={{ y: 200, duration: 500 }} class="bg-white rounded-xl shadow-2xl overflow-hidden p-4">
    
    <!-- Display -->
    <div class="flex flex-col h-[94px] text-right bg-gray-50 p-4 mb-4">
      <div class="h-5 text-zinc-500 text-sm">{numberInput}</div>
      {#if total !== 0}
        <div transition:fly={{ x: 10, duration: 800 }} class="text-zinc-800 text-right text-4xl font-light">
          {total}
        </div>
      {/if}
    </div>

    <!-- Add-Ons -->
    <div class="flex gap-4 items-center justify-around mb-2">
    <!-- Hx button -->
    <button 
      class="mb-2 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors active:scale-95"
      onclick={() => showHistory = !showHistory}
    >
      {showHistory ? 'Hide History' : 'Show History'}
    </button>
    <!-- Extension Button -->
    <button class="mb-2 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors active:scale-95"
      onclick={() => showExtended = !showExtended}
    >
      {showExtended ? 'Reduce' : 'Extend'}
    </button>
    </div>

    <!-- History -->
    {#if showHistory}
      <div transition:fly={{ y: -20, duration: 300 }} class="bg-gray-50 rounded-lg p-4 mb-4 max-h-48 overflow-y-auto">
        <h3 class="text-sm font-semibold text-zinc-700 mb-2">Calculation History</h3>
        {#if history.length === 0}
          <p class="text-xs text-zinc-400 italic">No history yet</p>
        {:else}
          <ul class="space-y-1">
            {#each history as entry, i}
              <li class="text-sm text-zinc-600 hover:bg-gray-200 p-1 rounded cursor-pointer">
                {entry}
              </li>
            {/each}
          </ul>
        {/if}
      </div>
    {/if}
      
    <!-- Keypad -->
    {#if showExtended}
      <ScientificCalculator {addToEquation} {clear} {calculate} {deleteLast}/>
    {:else}
      <BasicCalculator {addToEquation} {clear} {calculate}/>
    {/if}
  </div>
  </div>
</main>