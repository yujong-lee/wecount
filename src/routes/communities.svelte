<style lang="postcss">
  .container {
    background-color: var(--paper);
    filter: drop-shadow(0px 2.88727px 2.88727px rgba(255, 255, 255, 0.06));

    display: grid;
    justify-content: center;
    grid-auto-flow: row-dence;
    grid-auto-rows: min-content;
    grid-template-columns: repeat(auto-fill, minmax(460px, auto));
    gap: 24px;
    padding: 24px;
  }
</style>

<script lang="ts">
  import type {definitions} from '../types/supabase';
  import {_} from 'svelte-i18n';
  import InfiniteLoading from 'svelte-infinite-loading';
  import {getCommunities} from '../services/communityService';
  import CommunityItem from '../layouts/community-item.svelte';

  let communities: definitions['Community'][] = [];
  let newBatch: definitions['Community'][] = [];

  const getCursor = (): string | undefined => {
    if (communities.length === 0) {
      return new Date().toISOString();
    }

    return communities[communities.length - 1].createdAt;
  };

  async function fetchCommunities() {
    newBatch = (await getCommunities(getCursor())) || [];

    return newBatch;
  }

  // @ts-ignore
  async function infiniteHandler({detail: {loaded, complete}}) {
    const communites = await fetchCommunities();

    if (communites.length === 0) {
      // eslint-disable-next-line @typescript-eslint/no-unsafe-return
      return complete();
    }

    // eslint-disable-next-line @typescript-eslint/no-unsafe-return
    return loaded();
  }

  $: communities = [...communities, ...newBatch];
</script>

<div class="container">
  {#each communities as community (community.id)}
    <CommunityItem community={community} />
  {/each}
  <InfiniteLoading on:infinite={infiniteHandler}>
    <div slot="noMore">
      {$_('community.no_more')}
    </div>
  </InfiniteLoading>
</div>
