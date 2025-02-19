<script lang="ts">
  import { twMerge } from "tailwind-merge";
  import Swatch from "$lib/Swatch.svelte";
  import { COLORS } from "$lib/colors";
  import { Tabs, Toggle } from "bits-ui";

  const colorsByGroup = Object.groupBy(Object.keys(COLORS), (varName) => {
    const regex = /--color-(\S+)-[0-9]{2,3}/;
    const matches = varName.match(regex);
    return matches?.at(1) ?? "unknown";
  });

  let displayMode = $state("hex");
  let showColorCode = $state(true);
</script>

<svelte:head>
  <title>Tailwind Colors</title>
</svelte:head>
<header
  class="mx-auto mb-2 flex min-h-32 max-w-4xl flex-col items-start justify-between gap-y-4 px-6 py-10 sm:flex-row sm:items-center"
>
  <div>
    <h1 class="mb-2 text-xl font-bold">Tailwind Colors Reference</h1>
    <p class="mb-1 text-sm text-zinc-400">
      Click to copy color in {displayMode} form.
    </p>
    <p class="text-sm text-zinc-400">
      Example: <span class="rounded bg-zinc-800 px-1 py-0.5 font-mono">
        {#if displayMode === "hex"}
          #00bc7d
        {:else if displayMode === "oklch"}
          oklch(0.696 0.17 162.48)
        {/if}
      </span>
    </p>
  </div>
  <div class="flex h-10 items-stretch gap-x-2 text-sm sm:h-11 sm:text-base">
    <Tabs.Root bind:value={displayMode}>
      <Tabs.List class="flex gap-2 rounded-lg bg-zinc-900 p-1.5 font-mono font-medium">
        <Tabs.Trigger
          value="hex"
          class="w-16 cursor-pointer rounded py-1 ring-blue-500 transition-transform outline-none hover:bg-zinc-800 focus-visible:ring-2 active:scale-95 data-[state=active]:bg-zinc-200 data-[state=active]:text-zinc-800"
          >hex
        </Tabs.Trigger>
        <Tabs.Trigger
          value="oklch"
          class="w-16 cursor-pointer rounded py-1 ring-blue-500 transition-transform outline-none hover:bg-zinc-800 focus-visible:ring-2 active:scale-95 data-[state=active]:bg-zinc-200 data-[state=active]:text-zinc-800"
        >
          oklch
        </Tabs.Trigger>
      </Tabs.List>
    </Tabs.Root>
    <div class="flex aspect-square h-full items-center justify-center rounded-lg bg-zinc-900 p-1.5">
      <Toggle.Root
        class="box-border flex cursor-pointer items-center justify-center rounded ring-blue-500 transition-colors outline-none hover:bg-zinc-700 focus-visible:ring-2 active:scale-95"
        bind:pressed={showColorCode}
      >
        {#if showColorCode}
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            class="size-auto p-1"
            ><path
              fill="currentColor"
              d="M11.83 9L15 12.16V12a3 3 0 0 0-3-3zm-4.3.8l1.55 1.55c-.05.21-.08.42-.08.65a3 3 0 0 0 3 3c.22 0 .44-.03.65-.08l1.55 1.55c-.67.33-1.41.53-2.2.53a5 5 0 0 1-5-5c0-.79.2-1.53.53-2.2M2 4.27l2.28 2.28l.45.45C3.08 8.3 1.78 10 1 12c1.73 4.39 6 7.5 11 7.5c1.55 0 3.03-.3 4.38-.84l.43.42L19.73 22L21 20.73L3.27 3M12 7a5 5 0 0 1 5 5c0 .64-.13 1.26-.36 1.82l2.93 2.93c1.5-1.25 2.7-2.89 3.43-4.75c-1.73-4.39-6-7.5-11-7.5c-1.4 0-2.74.25-4 .7l2.17 2.15C10.74 7.13 11.35 7 12 7"
            /></svg
          >
        {:else}
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="24"
            height="24"
            viewBox="0 0 24 24"
            class="size-auto p-1"
            ><path
              fill="currentColor"
              d="M12 9a3 3 0 0 0-3 3a3 3 0 0 0 3 3a3 3 0 0 0 3-3a3 3 0 0 0-3-3m0 8a5 5 0 0 1-5-5a5 5 0 0 1 5-5a5 5 0 0 1 5 5a5 5 0 0 1-5 5m0-12.5C7 4.5 2.73 7.61 1 12c1.73 4.39 6 7.5 11 7.5s9.27-3.11 11-7.5c-1.73-4.39-6-7.5-11-7.5"
            /></svg
          >
        {/if}
      </Toggle.Root>
    </div>
  </div>
</header>
<main class="w-full overflow-y-auto">
  <div
    class={twMerge(
      "scroll-color mx-auto grid w-max gap-x-3 gap-y-2 px-6 py-2 pb-12 sm:gap-x-3 sm:gap-y-3",
      "grid-cols-11",
    )}
  >
    {#each Object.entries(colorsByGroup) as [colorGroup, varNames]}
      {#if colorGroup !== "unknown" && varNames !== undefined}
        {#each varNames as varName}
          <Swatch colorVar={varName} {displayMode} showCode={showColorCode} />
        {/each}
      {/if}
    {/each}
  </div>
</main>
