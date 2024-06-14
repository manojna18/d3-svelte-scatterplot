<script>
  let xTicks = [0, 10000, 20000, 30000, 40000, 50000, 60000, 70000, 80000, 90000, 100000]

  export let height;
  export let xScale;
  export let width;
</script>

<g class='axis x' transform="translate(0, {height})">
  {#each xTicks as tick}
    <g class='tick' transform="translate({xScale(tick + 1000)}, 0)">
      <line x1="0" y1="0" x2="0" y2="6" stroke="hsla(212, 10%, 53%, 1)"/>
      <line x1="0" y1="0" x2={width - 10000} y2="0" stroke="lightgray"/>
      <text x="0" y="6" dy="9" dominant-baseline="middle" text-anchor="middle">{tick/1000}k</text>
    </g>
  {/each}
</g>
<text class="axis-title" y={height -9} x={width - 50} text-anchor="end">GDP Per Capita (current US$) &#8594</text>