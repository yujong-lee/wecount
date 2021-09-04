<style lang="postcss">
  .container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    form {
      width: 400px;

      flex: 1;
      align-self: center;
      padding: 80px;

      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;

      button {
        cursor: pointer;
      }

      div {
        width: 100%;

        label {
          width: 100%;
        }

        input {
          width: 100%;
        }
      }

      input[type='submit'] {
        background-color: var(--positive);
        cursor: pointer;
        width: 100%;
        color: white;
        font-size: 16px;
      }

      .btn-signout {
        background-color: var(--negative);
        width: 100%;
        font-size: 16px;
      }
    }
  }
</style>

<script lang="ts">
  import {uploadSingleImage} from '../../services/storageService';

  import {_} from 'svelte-i18n';
  import supabase from '../../lib/db';
  import {user} from '../../stores/sessionStore';

  let loading = true;
  let files: HTMLInputElement;
  let name = '';
  let displayName = '';
  let avatarUrl = '';

  // eslint-disable-next-line @typescript-eslint/no-unused-vars
  function getProfile(_node: HTMLFormElement): SvelteActionReturnType {
    try {
      loading = true;
      name = $user?.name || '';
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

  const onFileSelected = async (e: any): Promise<void> => {
    try {
      let image = e.target.files?.[0];

      if (!image) return;

      loading = true;

      avatarUrl = (await uploadSingleImage(image)) || '';

      // let reader = new FileReader();

      // reader.readAsDataURL(image);
      // reader.onload = async (e) => {
      //   // @ts-ignore
      //   const uri = e.target?.result;
      // };
    } catch (error) {
      console.error(error);
    } finally {
      loading = false;
    }
  };

  async function updateProfile() {
    try {
      loading = true;
      const currentUser = $user;

      const updates = {
        id: currentUser?.id,
        displayName,
        name,
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

<div class="container">
  <form use:getProfile on:submit|preventDefault={updateProfile}>
    <input
      bind:this={files}
      type="file"
      accept=".jpg, .jpeg, .png"
      disabled={loading}
      on:change={onFileSelected}
    />
    <div class="input">
      <label for="email">{$_('email')}</label>
      <input id="email" type="text" value={$user?.email} disabled />
    </div>
    <div class="input">
      <label for="username">{$_('display_name')}</label>
      <input id="username" type="text" bind:value={displayName} />
    </div>
    <div class="input">
      <label for="username">{$_('name')}</label>
      <input id="username" type="text" bind:value={name} />
    </div>
    <input
      style="margin-top: 8px;"
      type="submit"
      value={loading ? $_('loading') : $_('update')}
      disabled={loading}
    />
    <button
      class="btn-signout"
      style="margin-top: 8px;"
      on:click={signOut}
      disabled={loading}
    >
      {$_('sign_out')}
    </button>
  </form>
</div>
