<script>
  import { supabase } from '../supabaseClient'
  export let sessionName;
</script>
<style>
    * {box-sizing: border-box;}
    
    .header {
      overflow: hidden;
      background-color: #f1f1f11b;
      padding: 20px 10px;
    }
    
    .header-right {
      float: right;
    }
    
    @media screen and (max-width: 500px) {
      
      .header-right {
        float: none;
      }
    }
    </style>
    
    <div class="header">
      <img src="src/assets/logoicon.png" alt="logo" width="50px">
      <div class="header-right">
        {#if sessionName}
        <button type="button" class="button block" on:click={() => supabase.auth.signOut()}>
          Sign Out {sessionName}
        </button>
        {/if}
      </div>
    </div>