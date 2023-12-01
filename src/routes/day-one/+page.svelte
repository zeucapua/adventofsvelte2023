<script lang="ts">
  import { onMount } from "svelte";
  let data : Record<string, string | number>[] = $state([]);

  let name : string = $state("");
  let tally : number = $state(0);
  
  let naughty : boolean = $state(true);
  let nice: boolean = $state(true);

  function submit() {
    data = [...data, { name, tally }];
  }

  onMount( async() => {
    const response = await fetch("https://advent.sveltesociety.dev/data/2023/day-one.json");
    data = await response.json();
  });
</script>

{#snippet child(values)}
  <div class={`${values.tally as number <= 0 ? "border-red-500" : "border-green-500"} flex gap-4 border px-4 py-2`}>
    <p>{values.name}: {values.tally}</p>
  </div>
{/snippet}

<h1>Day 1</h1>
<details>
  <summary>Challenge</summary>
  The Elves have been tirelessly creating presents all year round. They’re right on schedule, but today they’ve run into a big problem; the ancient system for tracking who’s been naughty or nice is out of commission. With the hundreds of thousands of letters from children piling up alongside their records of good and bad deeds, the Elves are in dire need of a modern solution.
  <br />
  Your mission is to build a system for the elves, enabling them to input names and tally each childs deeds to keep track of whether they’re good or bad. You could even categorise these automatically as “naughty” and “nice.” Fortunately, the elves have been meticulous in their record-keeping and have a backup of all the current data in JSON format. You’ll need to import this data into your newly developed system.
  <br />
  Here is an example of what the Elves have stored:
  <br />
  <code>
  {`[
    { "name": "Emma", "tally": 32 },
    { "name": "Ethan", "tally": 14 },
    { "name": "Isabella", "tally": 70 },
    { "name": "Jayden", "tally": -16 },
    { "name": "Isabella", "tally": -59 },
    { "name": "Noah", "tally": 19 },
    { "name": "Mia", "tally": -37 },
    { "name": "Will", "tally": -20 },
    { "name": "Sam", "tally": -91 },
    { "name": "Brittney", "tally": -98 }
  ]`}
  </code>
</details>

<section class="flex gap-8 border border-white px-8 py-4 w-fit">
  <input type="text" bind:value={name} class="border border-white px-4 py-2 bg-transparent" />
  <input type="number" bind:value={tally} class="border border-white px-4 py-2 bg-transparent" />
  <button onclick={submit} class="border border-white px-4 py-2 rounded-xl">
    Submit 
  </button>
</section>

<div class="flex gap-4">
  <p>Filters:</p>
  <label>
    <input type="checkbox" bind:checked={naughty} />
    Naughty
  </label>
  <label>
    <input type="checkbox" bind:checked={nice} />
    Nice
  </label>
</div>
<section class="flex flex-col gap-2">
  {#if data}
    {#each data as c}
      {#if naughty && c.tally as number <= 0}
        {@render child(c)}
      {/if}
      {#if nice && c.tally as number >= 0}
        {@render child(c)}
      {/if}
    {/each}
  {/if}
</section>
