<script lang="ts">
  import { onMount } from 'svelte'
  import { supabase } from './supabaseClient'
  import AppHeader from "../src/lib/AppHeader.svelte"
  import type { AuthSession } from '@supabase/supabase-js'
  import Account from './lib/Account.svelte'
  import Login from './lib/Login.svelte'

  let session: AuthSession

  onMount(() => {
    supabase.auth.getSession().then(({ data }) => {
      session = data.session
    })

    supabase.auth.onAuthStateChange((_event, _session) => {
      session = _session
    })
  })
</script>
<AppHeader sessionName={session ? session.user.email : ''}/>
<div>
  {#if !session}
  <Login />
  {:else}
  
  <Account {session} />
  {/if}
</div>