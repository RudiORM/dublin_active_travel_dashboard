<script>
  // Import necessary stores, functions, and icons
  import { RegionID, sums } from "../../stores/region";
  import { format } from "d3";
  import { resetMap } from "../../stores/map";
  import { metricLabel } from "../../stores/filterData";
  import Fa from "svelte-fa";
  import {
    faBicycle,
    faPersonWalking,
    faHeartPulse,
    faSmog,
    faCarSide,
    faEuroSign
  } from "@fortawesome/free-solid-svg-icons";

  // Formatters
  let f = format(",.0f");
  // Exported props
  export let cmode;
  export let dmode;

  // State variables
  let sel = "";
  let txt = "";
  let tit = "";
  let string_16 = '';
  let string_delta = '';
  let location = 'Dublin';
  let mode;

  // Determine suffixes based on `cmode`
  $: string_16 = cmode === "2016" ? '_16' : '';
  $: string_delta = cmode === "Change" ? 'delta_' : '';

  // Determine mode based on `dmode`
  $: mode = dmode === 'School or College' ? 'School, college or childcare' : dmode;

  // Set location based on `RegionID`
  $: location = $RegionID === "999999" ? "Dublin" : $RegionID.ED_ENGLISH;

  // Set descriptive text and title based on selected metric
  $: {
    if ($RegionID === "999999") {
      txt = 'This view shows the <a href="https://www.cso.ie/en/releasesandpublications/ep/p-cpp7/census2022profile7-employmentoccupationsandcommuting/commutingtowork/" > Means of Travel data from the 2022 and 2016 census</a>, aggregated at local electoral level. This question asked people how they travelled to work, school or college. Note: the 2022 Census also included a value called "Work mostly from home", which may skew the results. Click on a <a style="font-weight:bold;color:#6d8495">statistic</a> for information on how it was calculated.';
      tit = "this view";
    }
    if (sel === "prem_deaths") {
      txt = "The number of premature deaths averted this year by walking or cycling to work. For cycling, <a href='https://www.ncbi.nlm.nih.gov/pmc/articles/PMC10546027/#:~:text=Compared%20with%20non%2Dactive%20commuting,41%25%20lower%20risk%20of%20mortality.' target='_blank'>biking to work reduces the risk of premature death by 41% </a>. Walking 150 mins a week reduces this by <a href='https://bjsm.bmj.com/content/57/15/979' target='_blank'> 16%</a>. The risk of premature death between 30-70 years was estimated at 10%, which in any one year is 0.25%.";
      tit = "premature deaths";
    }
    if (sel === "health_exp") {
      txt = "Total money saved this year by not commuting by car. Assumes each person cycling or walking would otherwise have a 10km commute 3.5 days a week. We use the <a href='https://www.tomtom.com/traffic-index/dublin-traffic/' target = '_blank'> calculator by TomTom</a> to estimate this cost at €401.50 per person.";
      tit = "Fuel savings";
    }
    if (sel === "co2") {
      txt = "Annual CO2 saved this year by not commuting by car. Assumes each person cycling or walking would otherwise have a 10km commute 3.5 days a week and a <a href='https://climate.ec.europa.eu/eu-action/transport/road-transport-reducing-co2-emissions-vehicles/co2-emission-performance-standards-cars-and-vans_en' target = '_blank'> carbon footprint for a car as 95g CO2/km</a>";
      tit = "CO2 saved";
    }
    if (sel === "cong") {
      txt = "Total time (in years) saved by Dubliners this year by not sitting in traffic. Assumes a 10km commute 3.5 days a week. A study by Tomtom found that a daily 10km commute in Dublin resulted in <a href='https://www.tomtom.com/traffic-index/dublin-traffic/' target='_blank'> 158 hours lost to congestion.</a>";
      tit = "traffic avoided";
    }
  }

  // Metric calculations based on RegionID, using `f` for formatting
  $: walk = $RegionID === "999999"
    ? `${f(100 * $sums[string_delta + "On foot - " + mode + "_pct" + string_16])}%`
    : `${f(100 * $RegionID[string_delta + "On foot - " + mode + "_pct" + string_16])}%`;

  $: cycle = $RegionID === "999999"
    ? `${f(100 * $sums[string_delta + "Bicycle - " + mode + "_pct" + string_16])}%`
    : `${f(100 * $RegionID[string_delta + "Bicycle - " + mode + "_pct" + string_16])}%`;

  $: prem_deaths_cycle = $RegionID === "999999"
    ? f(0.001025 * $sums[string_delta + "Bicycle - " + mode + string_16])
    : f(0.001025 * $RegionID[string_delta + "Bicycle - " + mode + string_16]);

  $: prem_deaths_walk = $RegionID === "999999"
    ? f(0.001025 * $sums[string_delta + "On foot - " + mode + string_16])
    : f(0.001025 * $RegionID[string_delta + "On foot - " + mode + string_16]);

  $: cycle_eur_saved = $RegionID === "999999"
    ? `€${f(401.45 * $sums[string_delta + "Bicycle - " + mode + string_16] / 1e6)}m`
    : `€${f(401.45 * $RegionID[string_delta + "Bicycle - " + mode + string_16])}`;

  $: walk_eur_saved = $RegionID === "999999"
    ? `€${f(401.45 * $sums[string_delta + "On foot - " + mode + string_16] / 1e6)}m`
    : `€${f(401.45 * $RegionID[string_delta + "On foot - " + mode + string_16])}`;

  $: co2_saved_year = $RegionID === "999999"
    ? f(0.3372 * $sums[string_delta + "On foot - " + mode + string_16])
    : f(0.3372 * $RegionID[string_delta + "On foot - " + mode + string_16]);

  $: co2_saved_year_bike = $RegionID === "999999"
    ? f(0.3372 * $sums[string_delta + "Bicycle - " + mode + string_16])
    : f(0.3372 * $RegionID[string_delta + "Bicycle - " + mode + string_16]);

  $: traffic_year_foot = $RegionID === "999999"
    ? f(0.00990867579908675 * $sums[string_delta + "On foot - " + mode + string_16])
    : f(0.00990867579908675 * $RegionID[string_delta + "On foot - " + mode + string_16]);

  $: traffic_year_cycle = $RegionID === "999999"
    ? f(0.00990867579908675 * $sums[string_delta + "Bicycle - " + mode + string_16])
    : f(0.00990867579908675 * $RegionID[string_delta + "Bicycle - " + mode + string_16]);
</script>


<div class='container'>
<div class="overall2" on:click={function () {
  sel = "";
  resetMap();
}}>
  <h2 class="dublin-header">{"Boundary Statistics by Region"}</h2>

  <div class="flex-items2" style='gap:0px'>

    <div class="population-box item" style='flex:1 1 50%'>
      {#if location === "Dublin"}
      <div class="text">
        <p class="label" style="font-size:.9rem">{$metricLabel +' Census'}</p>
        <p class="loc">{location}</p>
        <p class="label">{@html '\xa0'}</p>
      </div>

      {:else}
        <div
        class="text" >
        <p class="label" style="font-size:.9rem">{$metricLabel +' Census'}</p>
      <p class="loc">{location}</p>
        <p class="label">{@html '&#9204;'}</p>

      </div>
      {/if}
      
    </div>

    <div class="population-box item" style='flex:1 1 50%'>
      <div
        class="text"
      >
        <p class="label">Commuters</p>
        <p class="number-green">{cycle + ' '}<span style='font-size:1.2rem'> <Fa icon={faBicycle} /></span></p>
        <p class="number">{walk+ ' '}<span style='font-size:1.2rem'> <Fa icon={faPersonWalking} /></span></p>
      </div>
    </div>


  </div>

</div>







<div class="flex-items2">
  <div class="overall2" style="background-color:{sel=='prem_deaths'?"#a7c9de44":"#ffffffaa"}" on:click={function () {
    sel = "prem_deaths";
    resetMap();
  }}>
    <h2 class="dublin-header"><Fa icon={faHeartPulse} /> Health</h2>
    <div class="flex-items">
      <div class="population-box item">
        <div
          class="text"
       
        >
          <p class="label">Annual premature deaths avoided</p>
          <p class="number-green">{prem_deaths_cycle + ' '}<span style='font-size:1.2rem'> <Fa icon={faBicycle} /></span></p>
          <p class="number">{prem_deaths_walk + ' '}<span style='font-size:1.2rem'> <Fa icon={faPersonWalking} /></span></p>
        </div>
      </div>
      </div>

   
  </div>

  <div class="overall2" style="background-color:{sel=='health_exp'?"#a7c9de44":"#ffffffaa"}" on:click={function () {
    sel = "health_exp";
    resetMap();
  }}>
    <h2 class="dublin-header"><Fa icon={faEuroSign} /> Financial</h2>
    <div
    class="text"
  >
    <p class="label">Annual money saved on fuel</p>
    <p class="number-green">{cycle_eur_saved+ ' '}<span style='font-size:1.2rem'><Fa icon={faBicycle} /></span></p>
    <p class="number">{walk_eur_saved+' '}<span style='font-size:1.2rem'><Fa icon={faPersonWalking} /></span></p>
  </div>
  </div>
</div>

<div class="flex-items2">
  <div class="overall2" style="background-color:{sel=='co2'?"#a7c9de44":"#ffffffaa"}" on:click={function () {
    sel = "co2";
    resetMap();
  }}>
    <h2 class="dublin-header"><Fa icon={faSmog} /> CO2</h2>
    <div
      class="text"
    >
      <p class="label">Annual CO2 saved (Tonnes)</p>
      <p class="number-green">{co2_saved_year_bike + ' '}<span style='font-size:1.2rem'> <Fa icon={faBicycle} /></span></p>
      <p class="number">{co2_saved_year + ' '}<span style='font-size:1.2rem'> <Fa icon={faPersonWalking} /></span></p>
    </div>
  </div>

  <div class="overall2" style="background-color:{sel=='cong'?"#a7c9de44":"#ffffffaa"}" on:click={function () {
    sel = "cong";
  }}>
    <h2 class="dublin-header"><Fa icon={faCarSide} /> Congestion</h2>
    <div
      class="text"
    >
      <p class="label">Annual time savings (years) </p>
      <p class="number-green">{traffic_year_cycle + ' '}<span style='font-size:1.2rem'> <Fa icon={faBicycle} /></span></p>
      <p class="number">{traffic_year_foot + ' '}<span style='font-size:1.2rem'> <Fa icon={faPersonWalking} /></span></p>
    </div>
  </div>
</div>

<div class="overall2">
  <div class="text2">
    <h2 class="dublin-header">{"About " + tit}</h2>
    <div class="number2">{@html txt}</div>
  </div>
</div>

</div>

<style>

.container{
  height: 100svh;
  min-height: 100svh;
}

  .overall {
    border: 0px solid #f5f5f5;
    background: #ffffffaa;
    border-radius: 15px;
    box-shadow: 0px 0px 0px 0px rgba(0, 0, 0, 0);
    padding: 18px;
    margin-bottom: 20px;
  }

  .dublin-header {
    font-size: 1.2rem;
    font-style: normal;
    font-weight: 500;
    background-color: #a7c9de; 
    padding: 5px 0px 5px 5px;
    -moz-border-radius: 0px;
-webkit-border-radius: 15px 15px 0px 0px;
border-radius: 15px 15px 0px 0px; 
    text-indent: 10px;
    color: #324754;
  }

 

  .flex-items {
    display: flex;
    gap: 10px;
  }

  .flex-items2 {
    display: flex;
    gap: 20px;
  }

  .overall2 {
    background: #ffffffaa;
    width: 100%;
    border-radius: 15px;
    box-shadow: 0px 4px 4px 0px rgba(0, 0, 0, 0.1);
    padding-bottom: 2px;
    margin-bottom: 20px;
    cursor: pointer;
    
  }


  .item {
    display: flex;
    flex: 1 1 50%;
    gap: 6px;
  }

  .text {
    flex: 0 1 auto;
    text-transform: uppercase;
    padding: 10px;
    padding-left: 15px;
    padding-right: 15px;
margin-bottom: 0px;
  }

  .text2 {
    flex: 0 1 auto;
  }

  .number-green {
    font-size: 1.5rem;
    font-style: normal;
    text-transform: none;
    font-weight: 400;
    line-height: 137.5%; /* 41.25px */
    margin-bottom: 0;
    color: #955196;
  }

  .number {
    font-size: 1.5rem;
    font-style: normal;
    text-transform: none;
    font-weight: 400;
    line-height: 137.5%; /* 41.25px */
    margin-bottom: 0;
    color: #374c80
  }

  .number2 {
    font-size: 1rem;
    font-style: 300px;
    font-weight: 300;
    line-height: 137.5%; /* 41.25px */
    margin-bottom: 0;
    padding: 15px;
  }

  .loc {
    font-size: 1.2rem;
    font-style: normal;
    color: #374c80;
    font-weight: normal;
    line-height: 120%;
    margin-bottom: 5px;
  }

  .label {
    font-size: .9rem;
    font-style: normal;
    color: #6d8495;
    font-weight: 700;
    line-height: 130%; /* 24px */
    margin-bottom: 6px;
    margin-top: 2px;

  }

  @media screen and (max-width: 450px) {
    .dublin-header {
      font-size: 24px;
      line-height: 137.5%;
    }

    .number {
      font-size: 24px;
      line-height: 150%; /* 36px */
    }

    .label {
      font-size: 1px;
      line-height: 150%; /* 24px */
    }
    .flex-items {
      flex-direction: column;
      gap: 9px;
    }
    .item:not(:first-of-type) {
      border-top: 1px solid #ececec;
      padding-top: 10px;
    }
  }
</style>
