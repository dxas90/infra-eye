<script lang="ts">
import { Input, Select } from "flowbite-svelte"
import { SearchOutline } from "flowbite-svelte-icons"
import type { Writable } from "svelte/store"

export let kinds: string[]
export let namespaces: string[]
export let statuses: string[] = [
  "All statuses",
  "Ready",
  "NotReady",
  "Progressing",
  "Suspended"
]
export let kindFilter: Writable<string>
export let namespaceFilter: Writable<string>
export let statusFilter: Writable<string>
export let searchQuery: Writable<string>

$: kindOptions = kinds.map((k) => ({
  value: k,
  name: k === "all" ? "All kinds" : k
}))
$: namespaceOptions = namespaces.map((n) => ({
  value: n,
  name: n === "all" ? "All namespaces" : n
}))
$: statusOptions = statuses.map((s) => ({ value: s, name: s }))
</script>

<div class="rounded-lg p-4">
  <div class="flex flex-row gap-3 items-center">
    <div class="flex-1">
      <Input
        placeholder="Search resources..."
        size="md"
        bind:value={$searchQuery}
      >
        <svelte:fragment slot="left">
          <SearchOutline class="w-4 h-4" />
        </svelte:fragment>
      </Input>
    </div>

    <Select
      size="md"
      items={kindOptions}
      bind:value={$kindFilter}
      class="w-40"
    />

    <Select
      size="md"
      items={namespaceOptions}
      bind:value={$namespaceFilter}
      class="w-48"
    />

    <Select
      size="md"
      items={statusOptions}
      bind:value={$statusFilter}
      class="w-44"
    />
  </div>
</div>
