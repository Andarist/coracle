<script lang="ts">
  import {Address} from "@welshman/util"
  import {loadHandle} from '@welshman/app'
  import Content from "src/partials/Content.svelte"
  import Spinner from "src/partials/Spinner.svelte"
  import NoteDetail from "src/app/views/NoteDetail.svelte"
  import RelayDetail from "src/app/views/RelayDetail.svelte"
  import PersonDetail from "src/app/views/PersonDetail.svelte"
  import GroupDetail from "src/app/views/GroupDetail.svelte"
  import EventDetail from "src/app/views/EventDetail.svelte"

  export let entity, type, data, relays
</script>

{#if type === "nevent"}
  <NoteDetail id={data.id} {relays} />
{:else if type === "note"}
  <NoteDetail id={data} {relays} />
{:else if type === "naddr"}
  {@const address = new Address(data.kind, data.pubkey, data.identifier).toString()}
  {#if [35834, 34550].includes(data.kind)}
    <GroupDetail {address} relays={data.relays} activeTab="notes" />
  {:else if data.kind === 31923}
    <EventDetail {address} relays={data.relays} />
  {:else}
    <NoteDetail {address} relays={data.relays} />
  {/if}
{:else if type === "nprofile"}
  <PersonDetail pubkey={data.pubkey} {relays} />
{:else if type === "npub"}
  <PersonDetail pubkey={data} />
{:else if entity.includes('@')}
  {#await loadHandle(entity)}
    <Spinner />
  {:then $handle}
    {#if $handle?.pubkey}
      <PersonDetail pubkey={$handle.pubkey} />
    {:else}
      <Content size="lg" class="text-center">
        <div>Sorry, we weren't able to find "{entity}".</div>
      </Content>
    {/if}
  {/await}
{:else}
  <Content size="lg" class="text-center">
    <div>Sorry, we weren't able to find "{entity}".</div>
  </Content>
{/if}
