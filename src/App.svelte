<script>
  export let searchInput = null;

  let fetching = false;
  let show = false;

  let randomAdvice;
  let search = false;
  let notFound = false;

  function fetchRandomAdvice() {
    fetching = true;
    show = false;
    notFound = false;
    if (searchInput !== null && searchInput !== "") {
      search = true;
      fetch(`https://api.adviceslip.com/advice/search/${searchInput}`)
        .then((result) => result.json())
        .then((data) => {
          if (data.slips) {
            randomAdvice = data.slips;
            show = true;
          } else {
            randomAdvice = data.message.text;
            show = true;
            search = false;
            notFound = true;
          }
        });
    } else {
      search = false;
      fetch(`https://api.adviceslip.com/advice`)
        .then((result) => result.json())
        .then((data) => {
          randomAdvice = data.slip.advice;
          show = true;
        });
    }
  }
</script>

<style>
  header,
  main,
  footer {
    text-align: center;
    padding: 1em;
    max-width: 280px;
    margin: 0 auto;
  }

  header img {
    max-width: 240px;
  }

  #adviceBox {
    margin: 2rem 0;
  }

  #adviceBox img {
    max-height: 160px;
  }

  #name,
  #searchInput {
    text-align: center;
  }

  h1 {
    color: #ff3e00;
    font-size: 2em;
    font-weight: 400;
    margin-bottom: 0;
  }

  .requestButton {
    font-weight: bold;
    color: white;
    background-color: #f17a18;
  }

  .adviceText {
    font-weight: 700;
    font-size: 1.5rem;
    margin: 6px 0;
    font-style: italic;
  }

  .requestWarningText,
  .adviceDate {
    font-size: 0.8rem;
  }

  .twitter-share-button {
    background-color: rgb(15, 133, 202);
    width: 100%;
    border-radius: 5px;
    padding: 5px;
  }
  .twitter-share-button a {
    color: white;
  }

  footer p {
    font-size: 0.9rem;
  }

  .littleFooter {
    font-size: 0.7rem;
  }

  @media screen and (min-width: 640px) {
    main {
      max-width: 600px;
    }
  }
</style>

<header>
  <h4>Advice Generator</h4>
  <img src="img/croods.png" alt="Header" />
</header>

<main>
  {#if !fetching}
    <section id="mainHeader">
      <h1>Hello, mate!</h1>
      <p>What are you up to today?</p>
    </section>
    <section id="mainSearch">
      <input
        id="searchInput"
        on:input={(e) => (searchInput = e.target.value)}
        type="text"
        value={searchInput} />
      <p style="font-size:0.8rem">
        Use the search box above to get advice by specific word
      </p>
      <button class="requestButton" on:click={() => fetchRandomAdvice()}>
        Give me an Advice
      </button>
    </section>
  {/if}

  {#if fetching}
    <section id="adviceBox">
      {#if !show}
        <p>Please Wait...</p>
      {/if}
      {#if show}
        {#if search}
          <p>Search Query : {searchInput}</p>
          {#each randomAdvice as entry}
            <p class="adviceText">"{entry.advice}"</p>
            <a
              target="_blank"
              rel="noopener noreferrer"
              href={`https://twitter.com/intent/tweet?text=${randomAdvice}`}>
              <button class="twitter-share-button">Tweet</button>
            </a>
            <p class="adviceDate">{entry.date}</p>
          {/each}
        {:else if !notFound}
          <p class="adviceText">"{randomAdvice}"</p>
          <a
            target="_blank"
            rel="noopener noreferrer"
            href={`https://twitter.com/intent/tweet?text=${randomAdvice}`}>
            <button class="twitter-share-button">Tweet</button>
          </a>
          <p class="requestWarningText">
            Wait for a few seconds before another
            <br />
            request to get a different one.
          </p>
        {:else}
          <p>{randomAdvice}</p>
        {/if}
        <img src="/img/croods-01.png" alt="Hello" />
      {/if}
    </section>
    <button
      on:click={() => {
        show = false;
        fetching = false;
        randomAdvice = undefined;
        searchInput = '';
      }}>
      Clear
    </button>
  {/if}
</main>

<footer>
  <p>
    2020 -
    <a href="https://sznm.dev" target="_blank" rel="noopener noreferrer">
      SZNM
    </a>
  </p>
  <p class="littleFooter">
    Built using Svelte. Powered by Advice Slip JSON API
  </p>
</footer>
