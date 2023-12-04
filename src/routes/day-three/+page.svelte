<script lang="ts">
  import { onMount } from "svelte";
  import { tweened } from "svelte/motion";
  import { fade } from "svelte/transition";
  import { quadInOut } from "svelte/easing";

  let uncategorized : Record<string, string | number>[] = $state([]);
  let added_gifts : Record<string, string | number>[] = $state([]);

  let operating_weight = $derived(getWeight());
  let status = $derived(operating_weight >= 100 ? "TOO HEAVY" : (operating_weight === 100 ? "PERFECT" : "READY"));

  let progress_tween = tweened(operating_weight, {
    duration: 400,
    easing: quadInOut
  });

  function getWeight() {
    let sum = 0;
    if (added_gifts) {
      added_gifts.map((g) => { sum += Number(g.weight); });
    }
    return sum;
  }

  onMount( async () => {
    const response = await fetch("https://advent.sveltesociety.dev/data/2023/day-three.json");
    uncategorized = await response.json();
  });
  
  function addGift(g : Record<string, string | number>) {
    added_gifts = [g, ...added_gifts]; 
    uncategorized = uncategorized?.filter((info) => info != g);
  }

  function removeGift(g : Record<string, string | number>) {
    uncategorized = [g, ...uncategorized]; 
    added_gifts = added_gifts?.filter((info) => info != g);
  }

  $effect(() => {
    progress_tween.set(operating_weight);
  });

</script>

{#snippet gift(values)}
  <div class="z-50 flex gap-8 w-fit min-w-lg border border-white items-center px-4 py-2">
    <p class="text-2xl">{values.weight}</p>
    <p>{values.name}</p>
  </div>
{/snippet}

<main class="flex flex-col p-8 gap-2 w-full min-w-screen h-screen">

  <!-- information -->
  <section class="basis-1/2 flex flex-col justify-between gap-8 border border-white p-8">
    <div>
      <h1 class="text-9xl font-black italic">
        {#if operating_weight > 100}
          TOO HEAVY
        {:else if operating_weight === 100}
          PERFECT
        {:else}
          READY
        {/if}
      </h1>

      <p class="text-4xl font-bold">{operating_weight.toFixed(2)} kg</p>
    </div>
    <progress value={$progress_tween} max="100" class="h-12"/>
  </section>

  <div class="basis-1/2 flex flex-row gap-4 max-h-full max-h-[50%]">
    <!-- added -->
    <section class="z-50 basis-1/2 flex flex-col gap-4 items-start border border-green-500 max-h-full overflow-y-scroll p-8">
      <div class="flex justify-between w-full items-center border-b-2 border-white pb-4">
        <h2 class="text-3xl font-bold">In Manifest</h2>
        <p>x {added_gifts.length}</p>
      </div>
      <!-- keyed item -->
      {#each added_gifts as g}
        <button onclick={() => removeGift(g)} transition:fade>
          {@render gift(g)}
        </button>
      {/each}
    </section>

    <!-- uncategorized -->
    <section class="z-50 grow flex flex-col gap-4 items-start border border-red-500 max-h-full overflow-y-scroll p-8">
      <div class="flex justify-between w-full items-center border-b-2 border-white pb-4">
        <h2 class="text-3xl font-bold">Cargo</h2>
        <p>x {uncategorized.length}</p>
      </div>
      <!-- keyed item -->
      {#each uncategorized as g (g.name)}
        <button onclick={() => addGift(g)} transition:fade>
          {@render gift(g)}
        </button>
      {/each}
    </section>
  <div>
</main>
