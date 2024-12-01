<script>
  import { onMount } from 'svelte';
  import scrollama from 'scrollama';
  import * as d3 from 'd3';
  import Bar from './Bar.svelte';
  import HorizontalBar from './HorizontalBar.svelte';

  export let data = [];
  export let fullData = [];
  let chartData = "Year"; // Determines which data to show in the bar chart

  let whichYear;
  let numBreaches;

  // Scroll filters and setup for Scrollama
  let activeSection = "all"; // Tracks the active scrollytelling section

  onMount(async () => {
    const table = await d3.csv('data_breaches.csv', (d) => ({
      ...d,
      'Entity': d['Entity'],
      'Year': +d['Year'],
      'NumRecords': +d['Records'],
      'OrgType': d['Organization type'],
      'Method': d['Method']
    }));
    data = table;
    fullData = data;
    numBreaches = data.length;
    whichYear = d3.min(data, d => d['Year']);
    data = data.filter(d => d['Year'] >= whichYear);
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
  let variable2 = 'OrgType';
  let variable3 = 'Method';

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
    } else if (activeSection === "orgType") {
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
      <h1>Data Breaches</h1>
      <h4>Ram M. Kripa</h4>

      <p>In an era where our personal data is more valuable than ever, data breaches have become a pressing concern. From healthcare institutions to financial services and even social media platforms, organizations across various sectors are increasingly targeted by malicious actors seeking access to sensitive information. The impacts are far-reaching, affecting millions of individuals whose personal details—ranging from names and addresses to credit card numbers and social security information—are exposed or stolen. </p>

      <p>This article delves into the trends and patterns of data breaches over time, exploring how these incidents have evolved and which types of organizations have been most affected. Through interactive visualizations, we aim to shed light on: </p>
      <ul>
        <li>Data Breaches Over Time: How have data breaches grown or changed in frequency over the years? Are there specific periods where breaches spiked, and what might have caused these increases?</li>
        <li>Organization Types: Which industries and sectors are most vulnerable to data breaches? We’ll examine how certain sectors, like healthcare and finance, have become prime targets and why.</li>
        <li>Methods of Breach: Discover the tactics used by attackers, from hacking and phishing to physical theft, and see which methods have become more or less prevalent over time.</li>
      </ul>
      <p>As you scroll through, interact with the charts to filter by different organization types or breach methods, allowing you to explore trends in greater depth. Together, let’s uncover the story behind the data and better understand the scope and impact of data breaches on our digital world. </p>
      <p>Explore data breaches over time and by different categories...</p>
    </section>
    <section class="scroll__text" data-section="all">
      <h2>Breaches Over Time</h2>
      <p>Since {whichYear}, there have been {numBreaches} data breaches recorded.</p>
      <p>The chart beside shows the number of data breaches over time.</p>
      <p> The number of data breaches in a year peaked in 2011, with over 40 breaches reported. The years 2015 and 2019 also saw significant spikes in data breaches, with over 30 incidents each. These peaks may be attributed to a variety of factors, including increased cyberattacks, changes in data protection laws, or the discovery of vulnerabilities in security systems. </p>
    </section>

    <section class="scroll__text" data-section="orgType">
      <h2>Breaches by Sector</h2>
      <p>The web sector saw the maximum number of breaches in this dataset obtained from kaggle, with healthcare as a close second.</p>
      <p>Some particularly famous examples of web data breaches include the <b>Yahoo breach</b> of 2013, where over 3 billion accounts were compromised, and the <b>Facebook-Cambridge Analytica scandal</b> in 2018, which exposed data from millions of users for political profiling.</p> <p>Some particularly famous examples of healthcare data breaches include the <b>Anthem breach</b> in 2015, which affected nearly 79 million individuals, and the <b>Premera Blue Cross breach</b> in 2015, exposing sensitive information of about 11 million customers.</p>
      <p><i>Click on the Bars below to see the number of data breaches over time for specific sectors</i></p>
    </section>
    <HorizontalBar data={data} fullData={fullData} variable={variable2} bind:filter={filter2} update={updateData} />

    <section class="scroll__text" data-section="method">
      <h2>How did Breaches happen?</h2>
      <p>As digital threats evolve, so do the methods used by malicious actors to access sensitive information. Over the years, certain techniques have emerged as the most common ways data is stolen, each with its unique approach and impact. Here are the leading methods of data breaches.</p>
      <p><i>Click on the Bars below to see the number of data breaches over time caused by specific methods</i></p>
      <p><i>You can even click on the bars here and above to filter by both sector and method</i></p>
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
