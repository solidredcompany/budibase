<script>
  export let focused = false
  export let selected = false
  export let highlighted = false
  export let width = ""
  export let selectedUser = null
  export let error = null
  export let rowIdx
  export let defaultHeight = false
  export let center = false

  $: style = getStyle(width, selectedUser)

  const getStyle = (width, selectedUser) => {
    let style = `flex: 0 0 ${width}px;`
    if (selectedUser) {
      style += `--cell-color:${selectedUser.color};`
    }
    return style
  }
</script>

<div
  class="cell"
  class:selected
  class:highlighted
  class:focused
  class:error
  class:center
  class:default-height={defaultHeight}
  class:selected-other={selectedUser != null}
  on:focus
  on:mousedown
  on:mouseup
  on:click
  on:contextmenu
  {style}
  data-row={rowIdx}
>
  {#if error}
    <div class="label">
      {error}
    </div>
  {/if}
  <slot />
  {#if selectedUser && !focused}
    <div class="label">
      {selectedUser.label}
    </div>
  {/if}
</div>

<style>
  /* Cells */
  .cell {
    height: var(--row-height);
    border-bottom: var(--cell-border);
    border-right: var(--cell-border);
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: flex-start;
    color: var(--spectrum-global-color-gray-800);
    font-size: var(--cell-font-size);
    gap: var(--cell-spacing);
    background: var(--cell-background);
    position: relative;
    width: 0;
    --cell-color: transparent;
  }
  .cell.default-height {
    height: var(--default-row-height);
  }
  .cell.center {
    align-items: center;
  }

  /* Cell border */
  .cell.focused:after,
  .cell.error:after,
  .cell.selected-other:not(.focused):after {
    content: " ";
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
    border: 2px solid var(--cell-color);
    pointer-events: none;
    border-radius: 2px;
    box-sizing: border-box;
  }

  /* Cell border for cells with labels */
  .cell.error:after,
  .cell.selected-other:not(.focused):after {
    border-radius: 0 2px 2px 2px;
  }
  .cell[data-row="0"].error:after,
  .cell[data-row="0"].selected-other:not(.focused):after {
    border-radius: 2px 2px 2px 0;
  }

  /* Cell z-index */
  .cell.error,
  .cell.selected-other:not(.focused) {
    z-index: 1;
  }
  .cell.focused {
    z-index: 2;
  }
  .cell.focused {
    --cell-color: var(--spectrum-global-color-blue-400);
  }
  .cell.error {
    --cell-color: var(--spectrum-global-color-red-500);
  }
  .cell:not(.focused) {
    user-select: none;
  }
  .cell:hover {
    cursor: default;
  }
  .cell.highlighted:not(.focused) {
    --cell-background: var(--cell-background-hover);
  }
  .cell.selected:not(.focused) {
    --cell-background: var(--spectrum-global-color-blue-100);
  }

  /* Label for additional text */
  .label {
    position: absolute;
    bottom: 100%;
    left: 0;
    padding: 1px 4px 3px 4px;
    margin: 0 0 -2px 0;
    background: var(--user-color);
    border-radius: 2px;
    display: block;
    color: white;
    font-size: 12px;
    font-weight: 600;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
    user-select: none;
  }
  .cell[data-row="0"] .label {
    bottom: auto;
    top: 100%;
    border-radius: 0 2px 2px 2px;
    padding: 2px 4px 2px 4px;
    margin: -2px 0 0 0;
  }
  .error .label {
    background: var(--spectrum-global-color-red-500);
  }
</style>
