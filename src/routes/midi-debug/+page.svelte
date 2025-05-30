<script lang="ts">
  import { Switch } from "@skeletonlabs/skeleton-svelte";

  import { chordsByNotes, createChordMapKey, midiNumberToNoteName } from "$lib/chords";
  import PianoRoll from "$lib/components/PianoRoll.svelte";
  import { getPressedKeys } from "$lib/context-midi";

  let pressedKeys = getPressedKeys();
  let showNames = $state(true);
  let showSharps = $state(true);
  let pianoRollShowNames = $derived<boolean | "withNumbers">(showNames ? "withNumbers" : false);

  let chordKey = $derived(createChordMapKey(pressedKeys));
  let possibleChords = $derived(chordsByNotes.get(chordKey));
  let nameOptions = $derived({ sharps: showSharps });

  let possibleChordShortNames = $derived(
    possibleChords?.map((chord) => `${chord.shortName(nameOptions)}`).join(", "),
  );
  let possibleChordLongNames = $derived(
    possibleChords?.map((chord) => `${chord.name(nameOptions)}`).join(", "),
  );
  let pressedKeyNames = $derived(
    Array.from(pressedKeys)
      .map((key) => midiNumberToNoteName(key, { sharps: showSharps }))
      .join(", "),
  );
</script>

<main>
  <PianoRoll {pressedKeys} {showSharps} showNames={pianoRollShowNames} />

  <div class="table-wrap">
    <table class="table">
      <tbody>
        <tr>
          <th scope="row" class="text-surface-300" colspan="2">
            <span class="text-md font-bold underline">Options</span>
          </th>
        </tr>
        <tr>
          <th scope="row">
            <label for="showNoteNames" class="label cursor-pointer">Show Note Names?</label>
          </th>
          <td class="!text-right">
            <Switch
              ids={{ hiddenInput: "showNoteNames" }}
              checked={showNames}
              onCheckedChange={() => (showNames = !showNames)}
            />
          </td>
        </tr>
        <tr>
          <th scope="row">
            <label
              for="chordNotationUsesSharps"
              class="label cursor-pointer"
              class:text-surface-300={!showNames}
              class:line-through={!showNames}
            >
              Sharps instead of flats?
            </label>
          </th>
          <td class="!text-right">
            <Switch
              ids={{ hiddenInput: "chordNotationUsesSharps" }}
              disabled={!showNames}
              checked={showSharps}
              onCheckedChange={(e) => (showSharps = e.checked)}
            />
          </td>
        </tr>

        <tr>
          <th scope="row" class="text-surface-300" colspan="2">
            <span class="text-md font-bold underline">Info</span>
          </th>
        </tr>
        <tr>
          <th scope="row" class="text-surface-300">Pressed Keys</th>
          <td class="!text-right">{pressedKeyNames}</td>
        </tr>
        <tr>
          <th scope="row" class="text-surface-300">Possible Chords (short)</th>
          <td class="!text-right">{possibleChordShortNames}</td>
        </tr>
        <tr>
          <th scope="row" class="text-surface-300">Possible Chords (long)</th>
          <td class="!text-right">{possibleChordLongNames}</td>
        </tr>
      </tbody>
    </table>
  </div>
</main>
