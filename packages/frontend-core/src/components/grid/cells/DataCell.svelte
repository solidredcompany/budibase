<script>
  import { getContext } from "svelte"
  import GridCell from "./GridCell.svelte"
  import { getCellRenderer } from "../lib/renderers"
  import { derived, writable } from "svelte/store"

  const { rows, focusedCellId, focusedCellAPI, menu, config, validation } =
    getContext("grid")

  export let highlighted
  export let selected
  export let rowFocused
  export let rowIdx
  export let focused
  export let selectedUser
  export let column
  export let row
  export let cellId
  export let updateValue = rows.actions.updateValue
  export let invertX = false
  export let invertY = false

  const emptyError = writable(null)

  let api

  // Get the error for this cell if the row is focused
  $: error = getErrorStore(rowFocused, cellId)

  // Determine if the cell is editable
  $: readonly =
    column.schema.autocolumn ||
    column.schema.disabled ||
    (!$config.allowEditRows && row._id)

  // Register this cell API if the row is focused
  $: {
    if (focused) {
      focusedCellAPI.set(cellAPI)
    }
  }

  const getErrorStore = (selected, cellId) => {
    if (!selected) {
      return emptyError
    }
    return derived(validation, $validation => $validation[cellId])
  }

  const cellAPI = {
    focus: () => api?.focus(),
    blur: () => api?.blur(),
    onKeyDown: (...params) => api?.onKeyDown(...params),
    isReadonly: () => readonly,
    getType: () => column.schema.type,
    getValue: () => row[column.name],
    setValue: value => {
      validation.actions.setError(cellId, null)
      updateValue(row._id, column.name, value)
    },
  }
</script>

<GridCell
  {highlighted}
  {selected}
  {rowIdx}
  {focused}
  {selectedUser}
  error={$error}
  on:click={() => focusedCellId.set(cellId)}
  on:contextmenu={e => menu.actions.open(cellId, e)}
  width={column.width}
>
  <svelte:component
    this={getCellRenderer(column)}
    bind:api
    value={row[column.name]}
    schema={column.schema}
    onChange={cellAPI.setValue}
    {focused}
    {readonly}
    {invertY}
    {invertX}
  />
</GridCell>
