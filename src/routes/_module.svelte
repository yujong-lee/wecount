<style lang="postcss">
  .container {
    display: grid;
    grid-template-rows: 56px 1fr;
  }
</style>

<script lang="ts">
  import {goto, url} from '@roxi/routify';
  import {user} from '../stores/sessionStore';
  import Header from '../layouts/header/index.svelte';

  user.subscribe((user) => {
    if (user) {
      $goto($url('/'));
    }
  });
</script>

<div class="container">
  <Header />
  <slot />
</div>
