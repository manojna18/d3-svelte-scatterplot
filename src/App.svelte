<script>
// @ts-nocheck

  import data from './data/data.js';
  import {scaleLinear } from "d3-scale";
  import AxisX from './components/AxisX.svelte';
  import AxisY from './components/AxisY.svelte';
  import Tooltip from './components/Tooltip.svelte';
  import {fly} from 'svelte/transition';
  import Scrolly from "./components/Scrolly.svelte";

  //SVG dimensions:
  $: width = 400;
  let height = 500;

  $: {
    width = window.innerWidth;
  }

  $: console.log(width);

  const margin = {
    top: 20,
    right: 20,
    bottom: 20,
    left: 20
  }

  $: innerWidth = (width - margin.left) - margin.right;
  let innerHeight = height - margin.top - margin.bottom;

  

  $: console.log(width);

  const countryWithMinGDP = data.reduce((prev, current) => prev.GDP < current.GDP ? prev : current);

  const maxGDP = data.reduce((prev, current) => prev['GDP Per Capita'] > current['GDP Per Capita'] ? prev : current);
  
  const min = countryWithMinGDP['GDP Per Capita'];
  const max = maxGDP['GDP Per Capita'];
  console.log(min, max);

  

    // Update xScale only when width changes
   

  $: console.log(`innerWidth: ${innerWidth}`)
  let xScale = scaleLinear()
    .domain([min, max])
    .range([0, width - 100]);

    $: {
        xScale = scaleLinear()
            .domain([min, max])
            .range([0, innerWidth - 100]);
    }

  let yScale = scaleLinear()
    .domain([0, 100])
    .range([innerHeight, 0]);

 let hoveredData;
 console.log(hoveredData);
 let radius = 10;

 let currentStep;
 console.log(currentStep);

 let initialData = data.sort((a, b) => a.Percent - b.Percent);
 let renderedData = initialData;

 $: {
  if (currentStep === 0){
    renderedData = initialData.map(d => ({...d, 'GDP Per Capita': 50000, Percent: 50}))
    hoveredData = null;
  } else if (currentStep === 1){
    renderedData = initialData.map(d => ({...d, Percent: 50}))
    hoveredData = null;
  } else if (currentStep === 2 ){
    renderedData = initialData;
    const indiaIndex = initialData.findIndex((data) => data.Country === "India");
    hoveredData = renderedData[indiaIndex];
  }
 }

 const infoToShow = [
  "South Asia, Middle East and North Africa are among the regions of the world with lowest female participation in workforce, according to data from the World Bank. Women's participation in these regions is less than one-third of that of men."
 , "Generally, high income countries show higher female labor force participation, driven by their role in service sector. However, women's participation is also significantly high in low-income countries due to their involvement in agriculture."
 , "Among the low-middle income countries, India stands out with a significant gender gap of about 50%. Of the women who work, about 80% are from rural areas engaged in self-employment, casual labor or unpaid family work, according to the Asia Development Bank."
]

</script>



<section>
  <div class="sticky">
      <h2>Per capita income vs. Labor Force Participation of married women with advanced education (2022)</h2>
    <div class="chart-container" bind:clientWidth={width}>
      <!-- svelte-ignore a11y-no-static-element-interactions -->
      <svg on:mouseleave={() => hoveredData = null} {width} {height} >
        <g class='inner-chart' transform="translate({margin.right}, {margin.top})">
            <AxisX height={innerHeight} xScale={xScale} width={width}/>
            <AxisY width={width} yScale={yScale} />
          {#each renderedData as item}
            <!-- svelte-ignore a11y-no-static-element-interactions -->
            <!-- svelte-ignore a11y-mouse-events-have-key-events -->
            <!-- svelte-ignore a11y-no-noninteractive-tabindex -->
            <circle 
              in:fly={{ x: -10, opacity: 0, duration: 500 }}
              cx={xScale(item['GDP Per Capita'])}
              cy={yScale(item.Percent)}
              r={hoveredData == item ? radius * 2 : radius}
              opacity={hoveredData ? (hoveredData == item ? 1 : 0.45) : 0.95}
              fill={item['Country'] === "India" ? "orange" :"#CCCCFF"}
              stroke="black"
              stroke-width={1}
              transform="translate(15, 0)"
              on:mouseover={() => {currentStep >= 2 ? hoveredData = item : null;
                console.log(hoveredData);
              } }
              on:focus={() => (currentStep >= 2 ? hoveredData = item : null)}
              tabindex="0"
            />
          {/each}
        </g>
      </svg>
      {#if hoveredData}
        <Tooltip data={hoveredData} {xScale} {yScale}/>
      {/if}
    </div>
    <p id="source">Source: International Labour Organization, World Bank</p>
  </div>
  
  

  <div>
    <Scrolly bind:value={currentStep}>
      {#each infoToShow as text, i}
        <div class="step" class:active={currentStep===i}>
          <div class="step-content">
            <p>{text}</p>
          </div>
        </div>
      {/each}
    
    </Scrolly>
  </div>
</section>

<style>

  h2{
    text-align: left;
    margin: 0 0 0.5rem 0;
    font-size: 1.35rem;
    font-weight: 600;
  }

  :global(.tick text, .axis-title){
    font-weight: 400;
    font-size: 12px;
    fill: hsla(212, 10%, 53%, 1);
  }

  .chart-container{
    position: relative;
  }

  circle{
    transition: r 300ms ease, 
    opacity 500ms ease,
    cx 500ms ease, 
    cy 500ms ease;
    cursor: pointer;
  }

  .step{
    height: 90vh;
    opacity: 0.3;
    transition: opacity 300ms ease;
    display: flex;
    justify-content: center;
    place-items: center;
    pointer-events: none;
  }

  

  .step{
    z-index: 2;
    position: relative;
  }

  .step-content {
    padding: 0.75rem 1rem;
    border: 1px solid black;
    border-radius: 3px;
    background: white;
}

  .step.active{
    opacity: 1;
  }

  section {
    position: relative;
  }

  .sticky{
    position: sticky;
    top: 0;
  }

  #source{
    font-size: smaller;
    float: right;
  }

  
 
</style>
