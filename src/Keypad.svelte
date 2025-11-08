<script>
    // props
    export let expand = 1
    export let type = 'normal'
    export let clicked = () => {
        console.log('I am being clicked.')
    }
    
    // internal state
    const typeToClass = {
        clear: "bg-red-200",
        equal: "bg-blue-500",
        operator: "bg-blue-200",
        normal: "bg-white"
    }
    
    let bgClass = typeToClass[type]
</script>

<button 
    class="border border-gray-100 rounded-lg px-5 py-[22px] m-2 text-base active:bg-gray-300 {bgClass}" 
    style="grid-column: span {expand};" 
    on:click={clicked}
>
    <slot>C</slot>
</button>
