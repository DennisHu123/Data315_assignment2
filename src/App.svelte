<script>
  import * as d3 from 'd3';
	import {onMount} from 'svelte';
  import Map from './Map.svelte';
  import Histogram from './Histogram.svelte';

	let data = [];
  let fullData = [];
  let filter1 = [];
  let filter2 = [];
  let filter3 = [];
  let filter4 = [];
  let row = [];

  let var1 = 'tree_diameter';
  let var2 = 'Pct_health';
  let var3 = 'N_dead';
  let var4 = 'Pct_noproblems'

	onMount(async function() {
    // load data from csv (source: https://chicagohealthatlas.org/download)
    let table = d3.csv('2015_Street_Tree_BoroCensus.csv', (d) => ({
          ...d,
          'boro_ct': d['boro_ct'],
          'N_trees': d['N_trees'],
          'tree_diameter': +d['tree_diameter'],
          'N_dead': +d['N_dead'],
          'Pct_health': +d['Pct_health'],
          'Pct_noproblems': +d['Pct_noproblems']
        }));

    let geocoord = d3.json('NYC_census_tract.geojson')
      .then((d) => d.features);

    await Promise.all([table, geocoord]).then((values) => {
      //console.log(values);
      let table = values[0];
      let geocoord = values[1];
      console.log(table.length)
      console.log(geocoord.length)
      
      // join the variables we want to show on the map
      for (let i = 0; i < geocoord.length; i++) {
        let tract = geocoord[i].properties.BoroCT2020;
        let found = false;
        let j = 0;
        while (!found && table.length > j) {
          if (table[j].boro_ct == tract) {
            found = true;
            data.push(geocoord[i]);
            data[data.length - 1].properties['N_trees'] = table[j]['N_trees']
            data[data.length - 1].properties['N_dead'] = table[j]['N_dead']
            data[data.length - 1].properties['Pct_health'] = table[j]['Pct_health']
            data[data.length - 1].properties['Pct_noproblems'] = table[j]['Pct_noproblems']
            data[data.length - 1].properties['tree_diameter'] = table[j]['tree_diameter']
          } else {
            j++;
          }
        }
      }
      // console.log(data);
      fullData = [...data];
    });
    row = [data[0].properties.BoroCT2020, data[0].properties.NTAName, data[0].properties.N_trees, data[0].properties.N_dead, data[0].properties.tree_diameter, data[0].properties.Pct_health, data[0].properties.Pct_noproblems];
	});

  function updateData() {
    data = [...fullData];
    if (filter1.length > 0 ) {
      data = data.filter((d) => (d.properties[var1] >= filter1[0] && d.properties[var1] < filter1[1]));
    } 
    if (filter2.length > 0) {
      data = data.filter((d) => (d.properties[var2] >= filter2[0] && d.properties[var2] < filter2[1]));
    }
    if (filter3.length > 0) {
      data = data.filter((d) => (d.properties[var3] >= filter3[0] && d.properties[var3] < filter3[1]));
    } 
    if (filter4.length > 0) {
      data = data.filter((d) => (d.properties[var4] >= filter4[0] && d.properties[var4] < filter4[1]));
    }
  }

  function tractClick(data) {
    row = [data.properties.BoroCT2020, data.properties.NTAName, data.properties.N_trees, data.properties.N_dead, data.properties.tree_diameter, data.properties.Pct_health, data.properties.Pct_noproblems]
    console.log()
  }
</script>

<main>

  <div class="container">
    <div class='row'>
      <h1>2015 New York City Tree Census</h1>
    </div>
    <div class='row'>
      <h5>The choropleth map shows the number of trees in each tract.</h5>
      <h5>Click a tract on the map to view the detailed data of that tract</h5>
      <h5>Brush over the histograms to filter the data selected. Crossfiltering is implemented.</h5>
    </div>

    <div class='row'>
      <div class="map"><Map data={data} fullData={fullData} OnClick={tractClick}></Map></div>
    </div>

    <div class="row">
      <table class="mb-5">
        <tr>
          <th>Boro_Census_Tract</th>
          <th>NTAName</th>
          <th>Number of Trees</th>
          <th>Number of Dead Trees</th>
          <th>Average Diameter of Trees</th>
          <th>Ratio with Good Health</th>
          <th>Ratio without Problems</th>
        </tr>

        <tr>
          <td>{row[0]}</td>
          <td>{row[1]}</td>
          <td>{row[2]}</td>
          <td>{row[3]}</td>
          <td>{Math.round(row[4]* 100) / 100}</td>
          <td>{Math.round(row[5]* 100) / 100}</td>
          <td>{Math.round(row[6]* 100) / 100}</td>
        </tr>
    </div>

    <div class='row'>
      <div class="col-6">        
        <h5>The Average Diameter of Trees</h5>
        <div class="hist"><Histogram data={data} fullData={fullData} variable={var1} bind:filter={filter1} update={updateData}></Histogram></div>
      </div>
      <div class="col-6"> 
        <h5>The Ratio of Trees with Good Health Status</h5>
        <div class="hist"><Histogram data={data} fullData={fullData} variable={var2} bind:filter={filter2} update={updateData}></Histogram></div>
      </div>
    </div>

    <div class='row'>
      <div class="col-6">
        <h5>The number of Dead Trees</h5>
        <div class="hist"><Histogram data={data} fullData={fullData} variable={var3} bind:filter={filter3} update={updateData}></Histogram></div>
      </div>
      <div class="col-6">
        <h5>The Ratio of Trees Without Significant Problems (root,trunk,branch,etc.)</h5>
        <div class="hist"><Histogram data={data} fullData={fullData} variable={var4} bind:filter={filter4} update={updateData}></Histogram></div>
      </div>
    </div>
      
  </div>
    
</main>

<style>
  

  .map { 
    /* flex:1 1 auto; */
    flex-grow:0;
  }
			
  .hist { 
    /* flex:0 1 auto; */
    flex-grow:0;
  }
			
  table, th, td {
    border: 1px solid black;
  }
</style>