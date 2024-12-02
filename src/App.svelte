<script>
  import { onMount } from 'svelte';
  import scrollama from 'scrollama';
  import * as d3 from 'd3';
  import Bar from './Bar.svelte';
  import HorizontalBar from './HorizontalBar.svelte';

  export let data = [];
  export let fullData = [];
  let chartData = "Date"; // Determines which data to show in the bar chart

  let whichDate;
  let numBreaches;

  // Scroll filters and setup for Scrollama
  let activeSection = "all"; // Tracks the active scrollytelling section

  onMount(async () => {
    const table = await d3.csv('sample_user_data.csv', (d) => ({
      ...d,
      'Domain': d['Website'],
      'Date': d['Date'],
      'Category': d['Category'],
      'TimeSpent': +d['Time Spent']
    }));
    data = table;
    fullData = data;
    numBreaches = data.length;
    whichDate = d3.min(data, d => d['Date']);
    fullData = data;

    // Scrollama setup for scrollytelling
    const scroller = scrollama();
    scroller
      .setup({
        step: ".scroll__text",
        offset: 0.5,
        debug: false
      })
      .onStepEnter(response => {
        activeSection = response.element.dataset.section; // Update based on scrolled section
        applyScrollFilter();
      });
  });

  // Filter variables for interactivity
  let filter1 = [];
  let filter2 = [];
  let filter3 = [];
  let variable2 = 'Domain';
  let variable3 = 'Category';

  // Filter data based on user clicks in HorizontalBar or Scrollama
  function updateData() {
    if (filter1.length === 0 && filter2.length === 0 && filter3.length === 0) {
      data = fullData;
      return;
    }
    if (filter1.length != 0) {
      data = fullData.filter(d => filter1.includes(d['NumRecords']));
    }
    if (filter2.length != 0) {
      data = data.filter(d => filter2.includes(d[variable2]));
    }
    if (filter3.length != 0) {
      data = data.filter(d => filter3.includes(d[variable3]));
    }
  }

  // Applies filter based on the active scrollytelling section
  function applyScrollFilter() {
    if (activeSection === "all") {
      data = fullData; // No filters applied
    } else if (activeSection === "domain") {
      //filter2 = ["Healthcare"]; // Example filter by OrgType
    } else if (activeSection === "method") {
      //filter3 = ["Hacking"]; // Example filter by Method
    }
    updateData();
  }
</script>

<main class="container">
  <!-- Scrollable Text Container on the Left -->
  <div class="scroll__container">
    <section class="scroll__text" data-section="intro">
      <h3>Papaya Privacy Presents:</h3>
      <h1>Your Data Story</h1>

      <p>Some text introducing Papaya Privacy, as well as the data story the user is about to see</p>
      <p></p>
    </section>
    <section class="scroll__text" data-section="all">
      <h2>Your Browsing Data: Collection</h2>
      <p>Papaya Privacy collects your data for sharing through the <a href="https://chromewebstore.google.com/detail/papaya-cookiemonster/llnpmgaejhooellpbbbicjhdfipckdka">Papaya CookieMonster Extension</a></p>

    </section>

    <section class="scroll__text" data-section="domain">
      <h2>Your Browsing Data: Most Visited Websites</h2>
      <p>Here are the most visited web domains by you</p>
    </section>
    <HorizontalBar data={data} fullData={fullData} variable={variable2} bind:filter={filter2} update={updateData} />

    <section class="scroll__text" data-section="method">
      <h2>Your Browsing Data: Most Visited Categories</h2>
      <p>Here are the most visited categories by you</p>
    </section>
    <HorizontalBar data={data} fullData={fullData} variable={variable3} bind:filter={filter3} update={updateData} />
  </div>

  <!-- Sticky Chart on the Right Showing Data Breaches Over Time -->
  <div class="sticky__chart">
    <Bar data={data} fullData={fullData} variable={chartData} />
  </div>
</main>

<style>
  .container {
    display: flex;
  }
  .scroll__container {
    width: 60%;
    padding: 20px;
    overflow-y: auto;
  }
  .scroll__text {
    padding: 40px 0;
    font-size: 1.2em;
  }
  .sticky__chart {
    position: sticky;
    top: 0;
    width: 40%;
    height: 100vh;
    padding: 20px;
  }
</style>
