<script>
  import { ActionButton } from "@budibase/bbui"
  import { getContext } from "svelte"

  const { dispatch, columns, stickyColumn, config, loaded } = getContext("grid")
</script>

<ActionButton
  icon="TableRowAddBottom"
  quiet
  size="M"
  on:click={() => dispatch("add-row")}
  disabled={!loaded ||
    !$config.allowAddRows ||
    (!$columns.length && !$stickyColumn)}
>
  Create row
</ActionButton>
