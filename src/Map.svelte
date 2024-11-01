<script>
    import * as d3 from 'd3';
    import {legendColor} from 'd3-svg-legend';

    export let data;
    export let fullData;
    export let OnClick;

    let width = 1200;
    let height = 600;

    let proj = d3.geoMercator()
        .scale(55000)
        .center([-73.9, 40.730610]);
    let path = d3.geoPath().projection(proj);

    $: scale = d3.scaleSequential(d3.interpolatePiYG)
        .domain([d3.min(data.map((d) => +d.properties.N_trees)), d3.mean(data.map((d) => +d.properties.N_trees)), d3.max(data.map((d) => +d.properties.N_trees))]);

    let legend;
    $: {	
            const colorLegend = legendColor()
                .shape('rect')
                .cells(10)
                .scale(scale);
            
            d3.select(legend)
                .call(colorLegend);
        }
    
    function DetectClick(d) {
      console.log(d.properties.N_trees);
      OnClick(d);
    }

</script>

<main>
    <div class = 'container_map'>
      <svg id="map" {width} {height}>
        {#each data as d}
          <path style = "fill: {scale(+d.properties.N_trees)};"
            d={path(d)}/>
        {/each}
        {#each fullData as d}
          <!-- svelte-ignore a11y-click-events-have-key-events -->
          <!-- svelte-ignore a11y-no-static-element-interactions -->
          <path class = "geooverlay"
            d={path(d)}  on:click={() => DetectClick(d)}/>
        {/each}
        <g transform="translate({width -100}, 50)"
              bind:this={legend} />
      </svg>
    </div>

</main>

<style>
    .container_map {
        padding : 0px;
    }

    .geooverlay {
      stroke: grey;
      stroke-width: 1px;
      fill: none;
    }

    .geooverlay:hover {
      fill: blue;
    }

  </style>