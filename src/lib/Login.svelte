<script lang="ts">
    import { supabase } from '../supabaseClient'
  
    let loading = false
    let email = ''
  
    const handleLogin = async () => {
      try {
        loading = true
        const { error } = await supabase.auth.signInWithOtp({ email })
        if (error) throw error
        alert('Check your email for login link!')
      } catch (error) {
        if (error instanceof Error) {
          alert(error.message)
        }
      } finally {
        loading = false
      }
    }
  </script>
  
  <div class="row flex-center flex" style="justify-content: center;min-height: 90vh;align-items: center;">
    <div class="col-4 form-widget" aria-live="polite">
      <p class="description">Sign in via magic link with your email below</p>
      <form class="form-widget" on:submit|preventDefault="{handleLogin}">
        <div>
          <label class="mr-2" for="email">Email</label>
          <input
            id="email"
            class="inputField"
            type="email"
            bind:value="{email}"
          />
        </div>
        <div class="button-div">
          <button type="submit" class="button block" aria-live="polite" disabled="{loading}">
            <span>{loading ? 'Loading' : 'Send magic link'}</span>
          </button>
        </div>
      </form>
    </div>
  </div>
  <style>
    #email {
      border: none;
      background: transparent;
      border-bottom: 1px solid #ccc;
      outline: none;
      color: black;
      width: 80%;
      vertical-align: super;
    }
    
    .button-div {
      text-align: center;
      margin-top: 40px;
    }
    ::-webkit-input-placeholder {
      color: #fff;
    }

    :-moz-placeholder { /* Firefox 18- */
      color: #fff;  
    }

    ::-moz-placeholder {  /* Firefox 19+ */
      color: #fff;  
    }

    :-ms-input-placeholder {  
      color: #fff;  
    }
  </style>