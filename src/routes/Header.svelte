<script lang="ts">
  import { base } from "$app/paths";
  import { page } from "$app/state";
  import { ArrowLeftIcon, SettingsIcon } from "@lucide/svelte";
  import { AppBar } from "@skeletonlabs/skeleton-svelte";

  import { routes } from "$lib/routes";
  import Settings from "../lib/components/Settings.svelte";

  let { classes = "" }: { classes?: string } = $props();
  let settingsOpen = $state(false);

  let title = $derived.by(() => {
    switch (page.url.pathname) {
      case routes.home:
        return "Chords";
      case routes.practiceSymbols:
        return "Practice - Chord Symbols";
      case routes.practiceNotes:
        return "Practice - Sheet Music";
      case routes.midiDebug:
        return "MIDI Debugger";
      default: {
        const parts = page.url.pathname.split("/");
        return parts[parts.length - 1];
      }
    }
  });
</script>

<svelte:head>
  <title>Chords | {title}</title>
</svelte:head>

<Settings bind:open={settingsOpen} />

<AppBar classes="sticky top-0 z-40 {classes}">
  {#snippet lead()}
    {#if page.url.pathname !== routes.home}
      <a href={base} class="btn preset-outlined-surface-500">
        <ArrowLeftIcon />
        <span>back</span>
      </a>
    {/if}
  {/snippet}
  {#snippet trail()}
    <button
      type="button"
      class="btn-icon preset-outlined-surface-500"
      onclick={() => (settingsOpen = true)}
    >
      <SettingsIcon />
    </button>
  {/snippet}
  <h1>{title}</h1>
</AppBar>
