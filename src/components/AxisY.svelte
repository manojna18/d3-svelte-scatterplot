<script>
  export let width;
  export let yScale;
  let yticks = yScale.ticks(10);
</script>

<g class='axis y'>
  {#each yticks as tick, index}
    <g class='tick' transform="translate(0, {yScale(tick)})">
      <line x1={0} x2={width - 40} y1={0} y2={0} stroke='lightgray'/>
      <text y={-3} x={-20}>
        {index === yticks.length - 1 ? `${tick}% Female workforce participation` : tick} 
      </text>
    </g>
  {/each}
  
</g>