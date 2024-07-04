<script>
  import { onMount } from 'svelte';
  let menus = [];
  let votes = [0, 0, 0];
  let menuUrls = [
      'https://widget.inisign.com/Widget/Customers/Customer.aspx?token=59db31f7-6775-43a1-a4bb-76a2bfb197ac&scaleToFit=true',
      'https://widget.inisign.com/Widget/Customers/Customer.aspx?token=aa1358ee-d30e-4289-a630-892cd1210857&scaleToFit=true',
      'https://widget.inisign.com/Widget/Customers/Customer.aspx?token=4a0457f8-dbfa-4783-8ebe-b5ee0486843f&scaleToFit=true'
  ];
  let buttonsDisabled = false;

  function fetchMenu(url, index) {
      fetch(url)
          .then(response => response.text())
          .then(data => {
              const parser = new DOMParser();
              const doc = parser.parseFromString(data, 'text/html');
              const menuItems = doc.querySelectorAll('.menu-item');
              const menu = Array.from(menuItems).map(item => item.querySelector('h2').textContent);
              menus[index] = menu;
          });
  }

  function vote(index) {
      votes[index]++;
      buttonsDisabled = true;
  }

  onMount(() => {
      menuUrls.forEach((url, index) => fetchMenu(url, index));
  });
</script>

<style>
  body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
  }
  .menu-container {
      display: flex;
      justify-content: space-around;
      width: 100%;
      padding: 20px;
  }
  .menu {
      border: 1px solid #ccc;
      padding: 10px;
      width: 30%;
  }
  .menu h1 {
      font-size: 1.5em;
      margin-bottom: 10px;
  }
  .menu h2 {
      font-size: 1.2em;
      margin: 5px 0;
  }
  .vote-button {
      display: block;
      margin: 10px 0;
      padding: 10px;
      background-color: #007BFF;
      color: white;
      border: none;
      cursor: pointer;
  }
  .vote-button:disabled {
      background-color: #ccc;
  }
  .results {
      margin-top: 20px;
  }
</style>

<h1>Stem på dagens lunch</h1>
<div class="menu-container">
  {#each menus as menu, index}
      <div class="menu">
          <h1>Menu {index + 1}</h1>
          {#each menu as item}
              <h2>{item}</h2>
          {/each}
          <button class="vote-button" on:click={() => vote(index)} disabled={buttonsDisabled}>Vote</button>
      </div>
  {/each}
</div>
<div class="results">
  <h2>Voting Results</h2>
  {#each menus as menu, index}
      <div>
          <h3>Menu {index + 1}: {votes[index]} votes</h3>
      </div>
  {/each}
</div>
