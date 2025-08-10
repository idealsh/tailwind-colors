<script lang="ts">
  import { twMerge } from "tailwind-merge";
  import { oklch, formatHex, formatCss } from "culori";
  import { COLORS } from "./colors";

  import { ClipboardCheck } from "@lucide/svelte";
  import { scale } from "svelte/transition";
  import { cubicInOut } from "svelte/easing";

  interface Props {
    colorVar: string;
    displayMode: string;
    showCode: boolean;
  }

  const { colorVar, displayMode, showCode }: Props = $props();

  const colorId = $derived(colorVar.substring("--color-".length));
  const colorName = $derived(colorVar.match(/--color-(\S+)-[0-9]{2,3}/)?.at(1));
  const colorLevel = $derived(colorVar.match(/--color-\S+-([0-9]{2,3})/)?.at(1));

  const colorHex = $derived(formatHex(COLORS[colorVar]));
  const colorOklch = $derived(oklch(COLORS[colorVar]));

  const luminance = $derived(oklch(COLORS[colorVar])?.l ?? 0);

  const textColorVar = $derived(`--color-${colorName}-${luminance < 0.69 ? 50 : 950}`);

  let showCopied = $state(false);
  let showCopiedTimeout: ReturnType<typeof setTimeout> | undefined;

  async function copyToClipboard(color: string) {
    try {
      await navigator.clipboard.writeText(color);

      if (showCopiedTimeout !== undefined) {
        clearTimeout(showCopiedTimeout);
        showCopiedTimeout = undefined;
      }

      showCopied = true;
      showCopiedTimeout = setTimeout(() => {
        showCopied = false;
        showCopiedTimeout = undefined;
      }, 1500);
    } catch (err) {
      console.error("Failed to copy: ", err);
    }
  }

  let transitionInProgress = $state(false);
</script>

{#if colorHex !== undefined}
  <button
    class={twMerge(
      "group cursor-pointer rounded ring-blue-500 ring-offset-2 ring-offset-neutral-900 transition-transform outline-none focus-visible:ring-2",
      !showCode && "active:scale-95",
    )}
    onclick={() => {
      if (displayMode === "hex") {
        copyToClipboard(colorHex);
      } else if (displayMode === "oklch" && colorOklch !== undefined) {
        copyToClipboard(formatCss(colorOklch));
      }
    }}
  >
    <div
      class={twMerge(
        "swatch h-9 w-full rounded border font-mono",
        "flex items-center justify-center px-2",
        showCode && "min-w-20",
      )}
      style:background-color="var({colorVar})"
      style:color="var({textColorVar})"
      style:border-color="color-mix(in oklab, var({textColorVar}) 20%, transparent)"
    >
      {#if colorOklch !== undefined}
        <span
          class="font-mono text-xs font-medium text-current/90 shadow-current transition-all duration-75 group-active:scale-95 group-active:font-bold"
        >
          {#if showCopied}
            <div
              class=""
              transition:scale={{ duration: 150, easing: cubicInOut }}
              onintrostart={() => (transitionInProgress = true)}
              onintroend={() => (transitionInProgress = false)}
              onoutrostart={() => (transitionInProgress = true)}
              onoutroend={() => (transitionInProgress = false)}
            >
              <ClipboardCheck class="size-5" />
            </div>
          {:else if showCode && !transitionInProgress}
            {#if displayMode === "oklch"}
              {colorOklch.l}
              {colorOklch.c}
              {colorOklch.h}
            {:else if displayMode === "hex"}
              {colorHex}
            {/if}
          {/if}
        </span>
      {/if}
    </div>
    <div class="mt-0.5 flex w-full flex-col items-start justify-center">
      <span class="text-xs tracking-wide text-neutral-400">{colorId}</span>
    </div>
  </button>
{/if}
