<script>
  import { fly } from 'svelte/transition'
  export let data;
  export let xScale;
  export let yScale;

  $: x = xScale(data["GDP Per Capita"]);
  $: y = yScale(data["Percent"]) + 50;

</script>

<div class="tooltip" style="position: absolute; top: {y}px; left: {x}px" transition:fly>
  <h1>{data.Country} - <span>{data.Percent}%</span></h1>
  <h2>{`GDP Per Capita: $${data["GDP Per Capita"]}`}</h2>
</div>

<style>
  .tooltip {
    padding: 8px 6px;
    background: rgb(248, 247, 233);
    box-shadow: rgba(0, 0, 0, 0.15) 2px 3px 8px;
    border-radius: 3px;
    pointer-events: none;
    transition: top 300ms ease, left 300ms ease;
}

span{
    background: #dda0dd50;
    font-size: 80%;
    margin-left: 2px;
    padding: 2px 4px;
    display: inline-block;
    vertical-align: bottom;
    border-radius: 3px;
    color: rgba(0, 0, 0, 0.7);
}

h1 {
    font-size: 1rem;
    font-weight: 500;
    margin-bottom: 6px;
    width: 100%;
}

h2 {
    font-size: 0.8rem;
    font-weight: 300;
    text-transform: uppercase;
}
</style>