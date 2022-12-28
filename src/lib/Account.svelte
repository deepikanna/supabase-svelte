<script lang="ts">
    import { onMount } from "svelte";
    import Select, { Option } from '@smui/select';
    import Textfield from '@smui/textfield';
    import {readable, get} from 'svelte/store'
    import type { AuthSession } from "@supabase/supabase-js";
    import { supabase } from '../supabaseClient'
    import 'ag-grid-community/styles/ag-grid.css';
  	import 'ag-grid-community/styles/ag-theme-alpine.css';
	  import AgGridSvelte from 'ag-grid-svelte';
  
    export let session: AuthSession;
    export let userDataGrid: any[] = [];
  
    let loading = false
    let focused = false;
    let pagination = true;
    let focusedID = false;
    let userName: string | null = null
    let id: string | null = null
    let departmentName: string = 'Employee'
    let sessionName = session.user.email;
    let departmentNamesAGList = ['Employee', 'HR','Admin','engineering'];

    const columnDefs = [
		{ field: "id" },
		{ field: "userName" },
		{ 
			field: "departmentName" ,
			cellEditor: 'agSelectCellEditor',
			cellEditorParams: {
				values: departmentNamesAGList,
			},
			onCellValueChanged: (
    			event) => updateDepartmemtName(event.data.id,event.newValue) ,
			editable: true
		}
	];
  
    onMount(() => {
        getUsers()
    })

    const getUsers = async () => {
      const { data, error, status } = await supabase
          .from('users')
          .select()
          
          userDataGrid = data;
          console.log(userDataGrid);
      supabase
  .channel('public:users')
  .on('postgres_changes', { event: '*', schema: 'public', table: 'users' }, payload => {
    console.log('Change received!', payload)
    userDataGrid = [...data, payload.new]
    console.log(userDataGrid);
  })
  .subscribe()
    }
    let api=userDataGrid, columnApi = columnDefs;
    const onGridReady = (event) => {
      api = event.api;
      columnApi = event.columnApi;
    };
    const updateDepartmemtName = async (id, departmentName) => {
      const updates = {
          id: id,
          departmentName
        }
  
        let { error } = await supabase.from('users').upsert(updates)
    }
    const updateUser = async () => {
      try {
        loading = true
        const { user } = session
  
        const updates = {
          id: id,
          userName,
          departmentName:departmentName,
          created_at: new Date().toISOString(),
        }
  
        let { error } = await supabase.from('users').upsert(updates)
  
        if (error) {
          throw error
        }
      } catch (error) {
        if (error instanceof Error) {
          alert(error.message)
        }
      } finally {
        loading = false
      }
    }
  </script>
  <div class="row" style="min-height: 89vh;">
    <form on:submit|preventDefault={updateUser} class="col-6">
      <div class="col-7 container form-widget ">
        <h5 style="font-weight: 700;">Insert Users</h5>
        <Textfield
          type="text"
          bind:value={id}
          label="User ID"
          style="min-width: 250px;"
          on:focus={() => (focusedID = true)}
          on:blur={() => (focusedID = false)}
        ></Textfield>
        <Textfield
          type="text"
          bind:value={userName}
          label="User Name"
          style="min-width: 250px;"
          on:focus={() => (focused = true)}
          on:blur={() => (focused = false)}
        ></Textfield>
        <Select bind:value={departmentName} label="Select Department">
          {#each departmentNamesAGList as dname}
            <Option value={dname}>{dname}</Option>
          {/each}
        </Select>
        <div class="mt-4">
          <button type="submit" class="button primary block" disabled={loading}>
            {loading ? 'Saving ...' : 'Update User'}
          </button>
        </div>
      </div>
      
      
    </form>
    {#if userDataGrid}
    <div style="height:630px;" class="ag-theme-alpine col-6">
      <AgGridSvelte bind:rowData={userDataGrid} {columnDefs} {onGridReady} bind:pagination={pagination} bind:paginationAutoPageSize={pagination}/>
    </div>
    {/if}
  </div>

  <style>
    .ag-theme-alpine {
      --ag-background-color: #ffffff36;
      --ag-border-color: transparent;
      --ag-header-background-color: #00f4ff1f;
      --ag-odd-row-background-color: #00f4ff1f;
      --ag-borders: solid 0px;
      padding: 50px;
    }
    .ag-theme-alpine :global(.ag-header-cell-label) {
      font-style: italic;
      color: #332fd5;
      font-weight: 800;
    }
    .ag-theme-alpine :global(.ag-center-cols-container) {
      width: auto !important;
      
    }
    .ag-theme-alpine :global(.ag-row) {
      color: #000;
      font-weight: 700;
    }
    :global(.mdc-select) {
      width: 78%;
    }

    form {
      display: flex;
      text-align: center;
      flex-direction: column;
      align-items: center;
      margin-top: 50px;
    }
    .form-widget {
      background-color: #00f4ff1f;
      padding: 2vh 6vh;
    }
  </style>