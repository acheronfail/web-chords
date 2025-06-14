<script lang="ts">
  import { Calendar } from "bits-ui";
  import { CalendarDate, getLocalTimeZone, today } from "@internationalized/date";

  import { user } from "$lib/svelte/stores.svelte";
  import { onMount } from "svelte";
  import { TrophyIcon, MedalIcon } from "@lucide/svelte";

  const tz = getLocalTimeZone();

  let {
    selectedDate = $bindable(today(tz)),
  }: {
    selectedDate?: CalendarDate;
  } = $props();

  let practiceSet = $derived(new Set(user.current.daysPracticed));

  let timer = (function dayTimer() {
    const nowMs = Date.now();
    const tomorrow = today(tz).add({ days: 1 });
    const tomorrowMs = tomorrow.toDate(tz).getTime();
    return setTimeout(
      () => {
        selectedDate = tomorrow;
        timer = dayTimer();
      },
      tomorrowMs - nowMs + 1_000,
    );
  })();

  onMount(() => () => clearTimeout(timer));
</script>

<Calendar.Root
  class="card border-surface-200-800 bg-surface-50-950 inline-block border p-2 shadow-xl"
  weekdayFormat="short"
  fixedWeeks={true}
  readonly
  type="single"
  bind:value={selectedDate}
>
  {#snippet children({ months, weekdays })}
    <Calendar.Header class="flex items-center justify-between">
      <Calendar.PrevButton class="btn-icon preset-outlined-surface-200-800">
        <span>&larr;</span>
      </Calendar.PrevButton>
      <Calendar.Heading
        class="cursor-pointer text-sm font-bold"
        title="Click to return to today"
        onclick={() => (selectedDate = today(tz))}
      />
      <Calendar.NextButton class="btn-icon preset-outlined-surface-200-800">
        <span>&rarr;</span>
      </Calendar.NextButton>
    </Calendar.Header>
    <div class="flex flex-col space-y-4 pt-4 sm:flex-row sm:space-y-0 sm:space-x-4">
      {#each months as month, i (i)}
        <Calendar.Grid class="w-full border-collapse space-y-1 select-none">
          <Calendar.GridHead>
            <Calendar.GridRow class="mb-1 flex w-full justify-between">
              {#each weekdays as day}
                <Calendar.HeadCell
                  class="text-surface-600-400 w-10 rounded-md text-xs font-normal!"
                >
                  <div>{day.slice(0, 2)}</div>
                </Calendar.HeadCell>
              {/each}
            </Calendar.GridRow>
          </Calendar.GridHead>
          <Calendar.GridBody>
            {#each month.weeks as weekDates}
              <Calendar.GridRow class="flex w-full">
                {#each weekDates as date}
                  {@const hasPracticed = practiceSet.has(date.toString())}
                  <Calendar.Cell
                    {date}
                    month={month.value}
                    class="relative size-10 p-0! text-center text-sm"
                  >
                    <Calendar.Day
                      class="data-selected:border-primary-300 data-today:border-surface-300 data-unavailable:text-surface-600-400 group relative inline-flex size-10 items-center justify-center rounded border border-transparent bg-transparent p-0 text-sm font-normal whitespace-nowrap data-disabled:pointer-events-none data-disabled:opacity-50 data-outside-month:pointer-events-none data-selected:font-bold data-unavailable:cursor-auto data-unavailable:border-none data-unavailable:line-through"
                    >
                      {#if hasPracticed}
                        <div
                          class="bg-success-900 absolute flex size-9 flex-col items-center justify-center rounded"
                          title="TODO: total score for the day"
                        >
                          <div class="mt-1 flex flex-row items-center justify-center gap-1">
                            <TrophyIcon class="text-amber-300" size={10} />
                            <MedalIcon class="text-amber-500" size={10} />
                          </div>
                          {date.day}
                        </div>
                      {:else}
                        {date.day}
                      {/if}
                    </Calendar.Day>
                  </Calendar.Cell>
                {/each}
              </Calendar.GridRow>
            {/each}
          </Calendar.GridBody>
        </Calendar.Grid>
      {/each}
    </div>
  {/snippet}
</Calendar.Root>
