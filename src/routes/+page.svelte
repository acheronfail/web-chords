<script lang="ts">
  import { Music4Icon, PianoIcon, TriangleAlertIcon, type IconProps } from "@lucide/svelte";
  import type { Component } from "svelte";
  import { fade } from "svelte/transition";

  import { routes } from "$lib/routes";
  import { settings } from "$lib/svelte/stores.svelte";

  interface CardProps {
    href: string;
    kind: string;
    title: string;
    description: string;
    highestScore?: number;
    lastPlayed?: Date;
    icon: Component<IconProps>;
  }

  let cards: CardProps[] = [
    {
      href: routes.practiceSymbols,
      icon: PianoIcon,
      kind: "Practise Chords",
      title: "Chord Symbols",
      description:
        "Practise your ability to recognise and play chords from Chord Symbols; things like Cmaj7, Dm7, G7, etc.",
    },
    {
      href: routes.practiceNotes,
      icon: Music4Icon,
      kind: "Practise Chords",
      title: "Sheet Music",
      description: "Practise your ability to recognise and play chords when notated on a staff.",
    },
  ];
</script>

{#snippet card({ kind, title, icon, href, description, highestScore, lastPlayed }: CardProps)}
  <a
    {href}
    class="card preset-filled-surface-100-900 border-surface-200-800 card-hover divide-surface-200-800 block divide-y overflow-hidden border"
  >
    <header>
      <div class="flex w-full items-center justify-center p-4">
        <svelte:component this={icon} size={48} />
      </div>
    </header>

    <article class="space-y-4 p-4">
      <div>
        <h2 class="h6">{kind}</h2>
        <h3 class="h3">{title}</h3>
      </div>
      <p class="text-surface-300">{description}</p>
    </article>

    {#if highestScore || lastPlayed}
      <footer class="flex items-center justify-between gap-4 p-4">
        {#if highestScore}
          <small class="text-surface-300">High Score: {highestScore}</small>
        {/if}
        {#if lastPlayed}
          <small class="text-surface-300">Last Played: {lastPlayed?.toLocaleDateString()}</small>
        {/if}
      </footer>
    {/if}
  </a>
{/snippet}

{#if settings.current.midiDeviceId === null}
  <div transition:fade class="p-4">
    <div
      class="card preset-outlined-warning-500 grid grid-cols-1 items-center gap-4 p-4 lg:grid-cols-[auto_1fr_auto]"
    >
      <TriangleAlertIcon />
      <div>
        <p class="font-bold">No MIDI Device configured!</p>
        <p class="text-xs opacity-60">Please open the settings and choose a MIDI device</p>
      </div>
    </div>
  </div>
{/if}

<div class="grid w-full grid-cols-2 gap-4 p-4">
  {#each cards as props}
    {@render card(props)}
  {/each}
</div>
