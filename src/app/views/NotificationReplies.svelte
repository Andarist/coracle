<script lang="ts">
  import {pluck} from "ramda"
  import {uniq} from "@welshman/lib"
  import {pubkey} from "@welshman/app"
  import {formatTimestamp} from "src/util/misc"
  import Spinner from 'src/partials/Spinner.svelte'
  import PeopleAction from "src/app/shared/PeopleAction.svelte"
  import Note from "src/app/shared/Note.svelte"
  import type {Notification} from "src/engine"
  import {deriveEvent} from 'src/engine'

  export let notification: Notification

  const {root, interactions, timestamp} = notification
  const event = deriveEvent(root)

  $: actionText = $event?.pubkey === $pubkey ? "replied to your note" : "mentioned you"
</script>

<div class="flex flex-col gap-4 my-4">
  <div class="flex items-center justify-between">
    <PeopleAction pubkeys={uniq(pluck("pubkey", interactions))} {actionText} />
    <small>{formatTimestamp(timestamp)}</small>
  </div>
  {#if $event}
    <Note topLevel depth={1} note={$event} filters={[{ids: pluck("id", interactions)}]} />
  {:else}
    <Spinner />
  {/if}
</div>
