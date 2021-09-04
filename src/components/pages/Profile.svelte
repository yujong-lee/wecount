<script lang="ts">
  import {_} from 'svelte-i18n';

  import supabase from '../../lib/db';
  import {user} from '../../stores/sessionStore';

  let loading = true;
  let displayName = '';
  let avatarUrl = '';

  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  function getProfile(_node: HTMLFormElement): SvelteActionReturnType {
    try {
      loading = true;
      displayName = $user?.displayName || '';
      avatarUrl = $user?.avatarUrl || '';

      return {
        destroy() {
          // the node has been removed from the DOM
        },
      };
    } catch (error) {
      alert(error.message);
    } finally {
      loading = false;
    }
  }

  async function updateProfile() {
    try {
      loading = true;
      const currentUser = $user;

      const updates = {
        id: currentUser?.id,
        displayName,
        avatarUrl,
        updated_at: new Date(),
      };

      let {error} = await supabase.from('profiles').upsert(updates, {
        returning: 'minimal', // Don't return the value after inserting
      });

      if (error) throw error;
    } catch (error) {
      alert(error.message);
    } finally {
      loading = false;
    }
  }

  async function signOut() {
    try {
      loading = true;
      let {error} = await supabase.auth.signOut();

      if (error) throw error;
    } catch (error) {
      alert(error.message);
    } finally {
      loading = false;
    }
  }
</script>

<form
  use:getProfile
  class="form-widget"
  on:submit|preventDefault={updateProfile}
>
  <div>
    <label for="email">Email</label>
    <input id="email" type="text" value={$user?.email} disabled />
  </div>
  <div>
    <label for="username">Name</label>
    <input id="username" type="text" bind:value={displayName} />
  </div>
  <div>
    <input
      type="submit"
      class="button block primary"
      value={loading ? $_('loading') : $_('update')}
      disabled={loading}
    />
  </div>
  <div>
    <button class="button block" on:click={signOut} disabled={loading}>
      {$_('sign_out')}
    </button>
  </div>
</form>
