<script>
  export let name;
  export let searchInput = null;

  let fetching = false;
  let show = false;

  let randomAdvice;
	let search = false;
	let notFound = false;

  function updateName(nameInput) {
    name = nameInput;
  }

  function fetchRandomAdvice() {
    fetching = true;
		show = false;
		notFound= false;
    console.log(searchInput);
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
      console.log("random");
      search = false;
      fetch(`https://api.adviceslip.com/advice`)
        .then((result) => result.json())
        .then((data) => {
          randomAdvice = data.slip.advice;
          show = true;
        })
    }
  }
</script>

<style>
  header,
  main,
  footer {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  #name,
  #searchInput {
    text-align: center;
  }

  h1 {
    color: #ff3e00;
    font-size: 2em;
    font-weight: 400;
  }

  .requestButton {
    font-weight: bold;
    color: white;
    background-color: #f17a18;
  }

  .adviceText {
    font-weight: 600;
    font-size: 1.5rem;
    margin: 6px 0;
  }

  .requestWarningText,
  .adviceDate {
    font-size: 0.8rem;
  }

  .twitter-share-button {
    background-color: rgb(15, 133, 202);
    border-radius: 5px;
    color: white;
    padding: 5px;
  }

  footer p {
    font-size: 0.9rem;
  }

  .littleFooter {
    font-size: 0.7rem;
  }

  @media (min-width: 640px) {
    main {
      max-width: 800px;
    }
  }
</style>

<header>
  <h4>Advice Generator</h4>
</header>

<main>
  <section>
    <h1>Hello, {name}!</h1>
    <input
      id="name"
      on:input={(e) => updateName(e.target.value)}
      type="text"
      placeholder="Your name here" />
  </section>

  <section>
    <p>What are you up to today?</p>
    <button class="requestButton" on:click={() => fetchRandomAdvice()}>
      Give me an Advice
    </button>
    <p style="font-size:0.8rem">
      Use this search box below to get advice by specific word
    </p>
    <input
      id="searchInput"
      on:input={(e) => (searchInput = e.target.value)}
      type="text"
      value={searchInput} />
  </section>

  {#if fetching}
    {#if !show}
      <p>Please Wait...</p>
    {/if}
    {#if show}
      <button
        on:click={() => {
          show = false;
          fetching = false;
          randomAdvice = undefined;
          searchInput = '';
        }}>
        Clear
      </button>
      {#if search}
        {#each randomAdvice as entry}
          <p class="adviceText">"{entry.advice}"</p>
          <a
            class="twitter-share-button"
            target="_blank"
            rel="noopener noreferrer"
            href={`https://twitter.com/intent/tweet?text=${entry.advice}`}>
            Tweet
          </a>
          <p class="adviceDate">{entry.date}</p>
        {/each}
      {:else}
				{#if !notFound}
        	<p class="adviceText">"{randomAdvice}"</p>
					<a
						class="twitter-share-button"
						target="_blank"
						rel="noopener noreferrer"
						href={`https://twitter.com/intent/tweet?text=${randomAdvice}`}>
						Tweet
					</a>
					<p class="requestWarningText">
						Wait for a few seconds before another
						<br />
						request to get a different one.
					</p>
				{:else}
					<p>{randomAdvice}</p>
				{/if}
      {/if}
    {/if}
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
