<script lang="ts">
  import "../../../../../util";
  import { LengthSelector } from "./types";
  import type { ParsedRunwayLength } from "./types";
  import type { InputResult } from "../../types";
  import Input from "../Input.svelte";

  export let parsed: ParsedRunwayLength | undefined = undefined;

  function validate(newValue: string): InputResult {
    if (newValue.length === 0) {
      parsed = undefined;
      return { kind: "ok", value: newValue };
    }

    let selector: LengthSelector;
    let slice: string;

    switch (newValue[0]) {
      case ">":
        [selector, slice] = [LengthSelector.GreaterThan, newValue.substr(1)];
        break;
      case "<":
        [selector, slice] = [LengthSelector.LessThan, newValue.substr(1)];
        break;
      default:
        if (!newValue[0].isCharDigit()) {
          parsed = undefined;
          return { kind: "err", value: "Valid selectors are < and >" };
        }

        [selector, slice] = [LengthSelector.Equal, newValue];
        break;
    }

    const isSliceEmpty = slice.length === 0;

    if (!isSliceEmpty && !slice.isDigits()) {
      parsed = undefined;

      return {
        kind: "err",
        value: "Can only contain selector (< or >) and digits",
      };
    }

    parsed = { length: isSliceEmpty ? 0 : Number(slice), selector };

    return { kind: "ok", value: newValue };
  }
</script>

<svelte:options accessors />

<Input
  name="length"
  label="Length"
  tooltip="Required runway length. Can be prefixed with < to exclude lengths less than the following number, or > for lengths greater than the following number. Example: The value >12500 will filter out airports that don't have any runways that are at least 12,500 feet long."
  {validate}
  value="" />