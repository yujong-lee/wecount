<style lang="postcss">
  .container {
    display: grid;
    justify-items: center;

    .wrapper {
      width: 100%;
      max-width: 800px;

      display: grid;
      row-gap: 20px;
      grid-template-rows: 80px 1fr;

      .header {
        display: grid;
        justify-items: right;
        align-items: center;

        .text {
          margin: 0 12px;
          color: white;
        }
      }

      .content {
      }
    }
  }
</style>

<script>
  import {_} from 'svelte-i18n';

  import Button from '../../../layouts/button.svelte';
</script>

<div class="container">
  <div class="wrapper">
    <div class="header">
      <Button primary type="button">
        <div class="text">
          {$_('feed.write')}
        </div>
      </Button>
    </div>
    <div class="content" />
  </div>
</div>
