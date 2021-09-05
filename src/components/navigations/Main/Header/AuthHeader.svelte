<script lang="ts">
  import {_} from 'svelte-i18n';
  import {push} from 'svelte-spa-router';
  import Button from '../../../uis/Button.svelte';
</script>

<Button
  on:click={async () => {
    await push('/sign_in');
  }}
  style="color: black; font-size: 14px; align-self: stretch;"
>
  <div class="text">{$_('login')}</div>
</Button>
<Button
  on:click={() => {
    // eslint-disable-next-line @typescript-eslint/no-floating-promises
    push('/sign_up');
  }}
  style="font-size: 14px; margin-left: 12px; align-self: stretch;"
>
  <div class="text">{$_('SignUp.title')}</div>
</Button>
