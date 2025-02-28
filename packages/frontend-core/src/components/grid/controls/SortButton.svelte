<script>
  import { getContext } from "svelte"
  import { ActionButton, Popover, Select } from "@budibase/bbui"

  const { sort, visibleColumns, stickyColumn } = getContext("grid")
  const orderOptions = [
    { label: "A-Z", value: "ascending" },
    { label: "Z-A", value: "descending" },
  ]

  let open = false
  let anchor

  $: columnOptions = getColumnOptions($stickyColumn, $visibleColumns)
  $: checkValidSortColumn($sort.column, $stickyColumn, $visibleColumns)

  const getColumnOptions = (stickyColumn, columns) => {
    let options = []
    if (stickyColumn) {
      options.push(stickyColumn.name)
    }
    return [...options, ...columns.map(col => col.name)]
  }

  const updateSortColumn = e => {
    sort.update(state => ({
      ...state,
      column: e.detail,
    }))
  }

  const updateSortOrder = e => {
    sort.update(state => ({
      ...state,
      order: e.detail,
    }))
  }

  // Ensure we never have a sort column selected that is not visible
  const checkValidSortColumn = (sortColumn, stickyColumn, visibleColumns) => {
    if (!sortColumn) {
      return
    }
    if (
      sortColumn !== stickyColumn?.name &&
      !visibleColumns.some(col => col.name === sortColumn)
    ) {
      if (stickyColumn) {
        sort.update(state => ({
          ...state,
          column: stickyColumn.name,
        }))
      } else {
        sort.update(state => ({
          ...state,
          column: visibleColumns[0]?.name,
        }))
      }
    }
  }
</script>

<div bind:this={anchor}>
  <ActionButton
    icon="SortOrderDown"
    quiet
    size="M"
    on:click={() => (open = !open)}
    selected={open || $sort.column}
    disabled={!columnOptions.length}
  >
    Sort
  </ActionButton>
</div>

<Popover bind:open {anchor} align="left">
  <div class="content">
    <Select
      placeholder={null}
      value={$sort.column}
      options={columnOptions}
      autoWidth
      on:change={updateSortColumn}
      label="Column"
    />
    <Select
      placeholder={null}
      value={$sort.order}
      options={orderOptions}
      autoWidth
      on:change={updateSortOrder}
      label="Order"
    />
  </div>
</Popover>

<style>
  .content {
    padding: 6px 12px 12px 12px;
    display: flex;
    align-items: center;
    gap: 8px;
  }
  .content :global(.spectrum-Picker) {
    width: 140px;
  }
</style>
