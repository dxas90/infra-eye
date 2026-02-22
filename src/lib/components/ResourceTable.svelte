<script lang="ts">
import FluxDetailsModal from "$lib/components/flux/FluxDetailsModal.svelte";
import type { K8sResource } from "$lib/stores/k8s-resources";
import { Badge } from "flowbite-svelte";

export let resources: K8sResource[]

let selectedResource: K8sResource | null = null
let showModal = false

function getResourceStatus(resource: K8sResource): {
  status: string
  color?: "red" | "green" | "yellow" | "blue" | "indigo" | "purple" | "pink"
  customClass?: string
} {
  const conditions = resource.status?.conditions || []
  const readyCondition = conditions.find((c: any) => c.type === "Ready")

  if (resource.spec?.suspend === true) {
    return {
      status: "Suspended",
      customClass: " text-slate-700"
    }
  }

  if (readyCondition?.status === "True") {
    return {
      status: "Ready",
      customClass: "bg-black text-white"
    }
  }

  if (readyCondition?.status === "False") {
    return { status: "NotReady", color: "red" }
  }

  return {
    status: "Progressing",
    customClass: "bg-amber-50 text-amber-900"
  }
}

function getStatusMessage(resource: K8sResource): string {
  const conditions = resource.status?.conditions || []
  const readyCondition = conditions.find((c: any) => c.type === "Ready")
  return readyCondition?.message || "-"
}

function handleRowClick(resource: K8sResource) {
  selectedResource = resource
  showModal = true
}
</script>

<div class="bg-white border  rounded-lg overflow-hidden">
  <div class="overflow-x-auto">
    <table class="w-full text-sm text-left">
      <thead class="text-xs text-slate-600 uppercase  border-b ">
        <tr>
          <th class="px-6 py-3 font-medium">Kind</th>
          <th class="px-6 py-3 font-medium">Namespace</th>
          <th class="px-6 py-3 font-medium">Name</th>
          <th class="px-6 py-3 font-medium">Status</th>
          <th class="px-6 py-3 font-medium">Message</th>
        </tr>
      </thead>
      <tbody>
        {#each resources as resource, i}
          {@const { status, color, customClass } = getResourceStatus(resource)}
          {@const message = getStatusMessage(resource)}
          <tr 
            class="border-b  hover: cursor-pointer transition-colors"
            onclick={() => handleRowClick(resource)}
          >
            <td class="px-6 py-3 font-medium text-slate-900">
              {resource.kind}
            </td>
            <td class="px-6 py-3 text-slate-900">
              {resource.metadata.namespace || "-"}
            </td>
            <td class="px-6 py-3 text-slate-900">
              {resource.metadata.name}
            </td>
            <td class="px-6 py-3">
              <Badge {color} class={customClass ? `${customClass} font-medium` : "font-medium"}>
                {status}
              </Badge>
            </td>
            <td class="px-6 py-3 text-slate-500 max-w-md truncate">
              {message}
            </td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
</div>

{#if selectedResource}
  <FluxDetailsModal resource={selectedResource} bind:open={showModal} />
{/if}