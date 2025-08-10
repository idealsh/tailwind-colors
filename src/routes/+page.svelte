<script lang="ts">
  import { twMerge } from "tailwind-merge";
  import Swatch from "$lib/Swatch.svelte";
  import { COLORS } from "$lib/colors";
  import { Tabs, Toggle } from "bits-ui";
  import { onMount } from "svelte";

  import { Eye, EyeOff } from "@lucide/svelte";

  const colorsByGroup = Object.groupBy(Object.keys(COLORS), (varName) => {
    const regex = /--color-(\S+)-[0-9]{2,3}/;
    const matches = varName.match(regex);
    return matches?.at(1) ?? "unknown";
  });

  let displayMode = $state("hex");
  let showColorCode = $state(true);

  let scrollElem = $state<HTMLElement>();

  onMount(() => {
    if (scrollElem) {
      console.log(scrollElem.scrollWidth, scrollElem.clientWidth);
      scrollElem.scrollTo({
        left: scrollElem.scrollWidth / 4,
      });
    }
  });
</script>

<svelte:head>
  <title>Tailwind Colors</title>
  <meta
    name="description"
    content="An interface for looking up and copying Tailwind v4 colors in both hex and oklch"
  />
</svelte:head>

<header
  class="mx-auto mb-2 flex min-h-32 max-w-4xl flex-col items-start justify-between gap-y-4 px-6 py-10 sm:flex-row sm:items-center"
>
  <div>
    <h1 class="mb-2 text-xl font-bold">Tailwind Colors Reference</h1>
    <p class="mb-1 text-sm text-neutral-600 dark:text-neutral-400">
      Click to copy color in {displayMode} form.
    </p>
    <p class="text-sm text-neutral-600 dark:text-neutral-400">
      Example: <span
        class="rounded bg-neutral-200 px-1 py-0.5 font-mono text-neutral-700 dark:bg-neutral-800 dark:text-inherit"
      >
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
      <Tabs.List
        class="flex gap-2 rounded-lg bg-neutral-200 p-1.5 font-mono font-medium dark:bg-neutral-800"
      >
        {#each ["hex", "oklch"] as format (format)}
          <Tabs.Trigger
            value={format}
            class={twMerge(
              "w-16 cursor-pointer rounded py-1 ring-blue-500 transition-transform outline-none",
              " hover:bg-neutral-100 focus-visible:ring-2 active:scale-95 dark:hover:bg-neutral-700",
              "data-[state=active]:bg-neutral-50 data-[state=active]:text-neutral-800",
              "dark:data-[state=active]:bg-neutral-300 dark:data-[state=active]:text-neutral-800",
            )}
          >
            {format}
          </Tabs.Trigger>
        {/each}
      </Tabs.List>
    </Tabs.Root>
    <div
      class="flex aspect-square h-full items-center justify-center rounded-lg bg-neutral-200 p-1.5 dark:bg-neutral-800"
    >
      <Toggle.Root
        class={twMerge(
          "box-border flex cursor-pointer items-center justify-center rounded",
          "ring-blue-500 transition-colors outline-none",
          "hover:bg-neutral-100 focus-visible:ring-2 active:scale-95 dark:hover:bg-neutral-600",
        )}
        bind:pressed={showColorCode}
      >
        <div class="size-auto p-1">
          {#if showColorCode}
            <EyeOff />
          {:else}
            <Eye />
          {/if}
        </div>
      </Toggle.Root>
    </div>
  </div>
</header>
<main class="w-full overflow-y-auto" bind:this={scrollElem}>
  <div
    class={twMerge(
      "scroll-color mx-auto grid w-max gap-x-4 gap-y-3 px-6 py-2 pb-12 sm:gap-x-4 sm:gap-y-3",
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
