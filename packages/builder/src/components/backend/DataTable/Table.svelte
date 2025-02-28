<script>
  import { fade } from "svelte/transition"
  import { goto, params } from "@roxi/routify"
  import { Table, Heading, Layout } from "@budibase/bbui"
  import Spinner from "components/common/Spinner.svelte"
  import CreateEditRow from "./modals/CreateEditRow.svelte"
  import CreateEditUser from "./modals/CreateEditUser.svelte"
  import {
    TableNames,
    UNEDITABLE_USER_FIELDS,
    UNSORTABLE_TYPES,
  } from "constants"
  import RoleCell from "./cells/RoleCell.svelte"
  import { createEventDispatcher } from "svelte"

  export let schema = {}
  export let data = []
  export let tableId
  export let title
  export let loading = false
  export let hideAutocolumns
  export let rowCount
  export let disableSorting = false
  export let customPlaceholder = false

  const dispatch = createEventDispatcher()

  let selectedRows = []
  let customRenderers = []

  $: selectedRows, dispatch("selectionUpdated", selectedRows)
  $: isUsersTable = tableId === TableNames.USERS
  $: data && resetSelectedRows()
  $: editRowComponent = isUsersTable ? CreateEditUser : CreateEditRow
  $: {
    UNSORTABLE_TYPES.forEach(type => {
      Object.values(schema || {}).forEach(col => {
        if (col.type === type) {
          col.sortable = false
        }
      })
    })
  }
  $: {
    if (isUsersTable) {
      customRenderers = [
        {
          column: "roleId",
          component: RoleCell,
        },
      ]
      UNEDITABLE_USER_FIELDS.forEach(field => {
        if (schema[field]) {
          schema[field].editable = false
        }
      })
      if (schema.email) {
        schema.email.displayName = "Email"
      }
      if (schema.roleId) {
        schema.roleId.displayName = "Role"
      }
      if (schema.firstName) {
        schema.firstName.displayName = "First Name"
      }
      if (schema.lastName) {
        schema.lastName.displayName = "Last Name"
      }
      if (schema.status) {
        schema.status.displayName = "Status"
      }
    }
  }

  const resetSelectedRows = () => {
    selectedRows = []
  }

  const selectRelationship = ({ tableId, rowId, fieldName }) => {
    $goto(
      `/builder/app/${$params.application}/data/table/${tableId}/relationship/${rowId}/${fieldName}`
    )
  }
</script>

<Layout noPadding gap="S">
  <Layout noPadding gap="XS">
    {#if title}
      <div class="table-title">
        <Heading size="M">{title}</Heading>
        {#if loading}
          <div transition:fade|local>
            <Spinner size="10" />
          </div>
        {/if}
      </div>
    {/if}
    <div class="popovers">
      <slot />
    </div>
  </Layout>
  {#key tableId}
    <div class="table-wrapper">
      <Table
        {data}
        {schema}
        {loading}
        {customRenderers}
        {rowCount}
        {disableSorting}
        {customPlaceholder}
        showAutoColumns={!hideAutocolumns}
        on:clickrelationship={e => selectRelationship(e.detail)}
        on:sort
      >
        <slot slot="placeholder" name="placeholder" />
      </Table>
    </div>
  {/key}
</Layout>

<style>
  .table-title {
    height: 24px;
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
  }
  .table-title > div {
    margin-left: var(--spacing-xs);
  }
  .table-wrapper {
    overflow: hidden;
  }

  .popovers {
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
    gap: var(--spacing-s);
  }
  .popovers :global(button) {
    font-weight: 600;
    margin-top: var(--spacing-l);
  }
  .popovers :global(button svg) {
    margin-right: var(--spacing-xs);
  }
</style>
