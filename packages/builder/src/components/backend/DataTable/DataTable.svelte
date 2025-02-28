<script>
  import { tables } from "stores/backend"
  import EditRolesButton from "./buttons/EditRolesButton.svelte"
  import { TableNames } from "constants"
  import { Grid } from "@budibase/frontend-core"
  import { API } from "api"
  import GridAddColumnModal from "components/backend/DataTable/modals/grid/GridCreateColumnModal.svelte"
  import GridCreateEditRowModal from "components/backend/DataTable/modals/grid/GridCreateEditRowModal.svelte"
  import GridEditUserModal from "components/backend/DataTable/modals/grid/GridEditUserModal.svelte"
  import GridCreateViewButton from "components/backend/DataTable/buttons/grid/GridCreateViewButton.svelte"
  import GridImportButton from "components/backend/DataTable/buttons/grid/GridImportButton.svelte"
  import GridExportButton from "components/backend/DataTable/buttons/grid/GridExportButton.svelte"
  import GridFilterButton from "components/backend/DataTable/buttons/grid/GridFilterButton.svelte"
  import GridManageAccessButton from "components/backend/DataTable/buttons/grid/GridManageAccessButton.svelte"
  import GridRelationshipButton from "components/backend/DataTable/buttons/grid/GridRelationshipButton.svelte"
  import GridEditColumnModal from "components/backend/DataTable/modals/grid/GridEditColumnModal.svelte"

  const userSchemaOverrides = {
    firstName: { name: "First name", disabled: true },
    lastName: { name: "Last name", disabled: true },
    email: { name: "Email", disabled: true },
    roleId: { name: "Role", disabled: true },
    status: { name: "Status", disabled: true },
  }

  $: id = $tables.selected?._id
  $: isUsersTable = id === TableNames.USERS
  $: isInternal = $tables.selected?.type !== "external"
</script>

<div class="wrapper">
  <Grid
    {API}
    tableId={id}
    allowAddRows={!isUsersTable}
    allowDeleteRows={!isUsersTable}
    schemaOverrides={isUsersTable ? userSchemaOverrides : null}
    on:updatetable={e => tables.updateTable(e.detail)}
  >
    <svelte:fragment slot="controls">
      {#if isInternal}
        <GridCreateViewButton />
      {/if}
      <GridManageAccessButton />
      {#if isUsersTable}
        <EditRolesButton />
      {/if}
      {#if !isInternal}
        <GridRelationshipButton />
      {/if}
      <GridImportButton disabled={isUsersTable} />
      <GridExportButton />
      <GridFilterButton />
      <GridAddColumnModal />
      <GridEditColumnModal />
      {#if isUsersTable}
        <GridEditUserModal />
      {:else}
        <GridCreateEditRowModal />
      {/if}
    </svelte:fragment>
  </Grid>
</div>

<style>
  .wrapper {
    flex: 1 1 auto;
    margin: -28px -40px -40px -40px;
    display: flex;
    flex-direction: column;
    background: var(--background);
    overflow: hidden;
  }
</style>
