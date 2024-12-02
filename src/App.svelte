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
     
      <h2>Papaya Privacy Presents:</h2>
      <h1>Your Data Story</h1>
      <p>
        At Papaya Privacy, we believe in empowering you with insights about your data. This story is a visualization of your browsing habits, based on data collected ethically and transparently through the <a href="https://chromewebstore.google.com/detail/papaya-cookiemonster/llnpmgaejhooellpbbbicjhdfipckdka" target="_blank">Papaya CookieMonster Extension</a>.
      </p>
      <p>
        Explore your browsing patterns, see the websites you visit most often, and uncover which categories of websites take up the majority of your time. This is your data story—yours to understand, control, and act upon.
      </p>
    </section>
    <section class="scroll__text" data-section="all">
      <h2>Data Collection</h2>

      <p>
        Over the past 30 days, <a href="https://chromewebstore.google.com/detail/papaya-cookiemonster/llnpmgaejhooellpbbbicjhdfipckdka">Papaya CookieMonster</a> has collected data on the websites you visit and how much time you spend on each. This data is collected with your consent and kept private until you choose to share it. The data shown here is anonymized and demonstrates how Papaya can visualize your browsing habits, without revealing any personally identifiable traits.
      </p>
      <p>
        On the right, you can see the number of websites you visited per day over the last month. Some days are busier than others—perhaps you’ll notice a spike in visits on workdays or during specific events.
      </p>

    </section>

    <section class="scroll__text" data-section="domain">
      <h2>Your Most Visited Websites</h2>
      <p>
        These are the 20 websites you visited most frequently over the past 30 days. Each bar represents the total number of visits to a specific domain, like <em>google.com</em> or <em>amazon.com</em>. 
      </p>
      <p>
        Do you notice any patterns? Perhaps certain websites dominate your daily routine, such as search engines, social media platforms, or e-commerce sites.
      </p>
      <p>
        Clicking on a bar will filter the data on the right, showing which days you visited that website, and how many times you did so.
      </p>
    </section>
    <HorizontalBar data={data} fullData={fullData} variable={variable2} bind:filter={filter2} update={updateData} />

    <section class="scroll__text" data-section="method">
      <h2>Most Visited Website Categories</h2>
      <p>
        Here, your browsing habits are grouped by categories, such as <strong>Social Media</strong>, <strong>News</strong>, or <strong>Shopping</strong>. This view shows which types of websites take up the most of your time.
      </p>
      <p>
        Are there any surprises?
      </p>
      <p>
        Clicking on a category will filter the chart on the right, showing your activity for that category over time.
      </p>
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
