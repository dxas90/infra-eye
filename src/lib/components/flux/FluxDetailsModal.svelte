<script lang="ts">
import type { K8sResource } from "$lib/stores/k8s-resources"
import { Badge, Modal } from "flowbite-svelte"
import {
  ChevronDownOutline,
  ChevronUpOutline,
  ClockOutline,
  CodeOutline,
  ExclamationCircleOutline
} from "flowbite-svelte-icons"
import { formatTime, getSourceInfo } from "./utils"

export let resource: K8sResource
export let open: boolean

let showConditions = false

const sourceInfo = getSourceInfo(resource)
const readyCondition = resource.status?.conditions?.find(
  (c: any) => c.type === "Ready"
)
const isReady = readyCondition?.status === "True"
const statusText = readyCondition?.message || "Unknown"
const conditions = resource.status?.conditions || []

const lastReconcile = formatTime(
  resource.status?.lastHandledReconcileAt ||
    resource.status?.lastAppliedRevision ||
    resource.status?.artifact?.lastUpdateTime
)
</script>

<Modal bind:open size="lg" class="">
	<!-- @ts-ignore - flowbite-svelte slot typing issue -->
	<div slot="header" class="flex items-center gap-2">
		<CodeOutline size="sm" />
		<span>{resource.metadata.name}</span>
		<Badge color={isReady ? "green" : "red"} class="text-xs">
			{isReady ? "Ready" : "Not Ready"}
		</Badge>
	</div>

	<div class="space-y-4">
		<!-- Metadata -->
		<div class="grid grid-cols-2 gap-4 text-sm">
			<div>
				<span class="">Kind:</span>
				<span class="ml-2 ">{resource.kind}</span>
			</div>
			<div>
				<span class="">Namespace:</span>
				<span class="ml-2 "
					>{resource.metadata.namespace || "default"}</span
				>
			</div>
			<div>
				<span class="">Created:</span>
				<span class="ml-2 "
					>{formatTime(resource.metadata.creationTimestamp)}</span
				>
			</div>
			<div>
				<span class="">Last Reconcile:</span>
				<span class="ml-2 ">{lastReconcile}</span>
			</div>
		</div>

		<!-- Source Information -->
		<div class="border-t  pt-4">
			<h4 class="text-sm font-semibold mb-3">Source Information</h4>
			<div class="space-y-2 text-sm">
				<div class="flex items-start">
					<span class=" w-24 flex-shrink-0">{sourceInfo.type}:</span>
					<span class=" break-all">{sourceInfo.value}</span>
				</div>
				{#if sourceInfo.version}
					<div class="flex items-start">
						<span class=" w-24 flex-shrink-0">Version:</span>
						<span class="">{sourceInfo.version}</span>
					</div>
				{/if}
				{#if sourceInfo.branch}
					<div class="flex items-start">
						<span class=" w-24 flex-shrink-0">Branch:</span>
						<span class="">{sourceInfo.branch}</span>
					</div>
				{/if}
				{#if sourceInfo.tag}
					<div class="flex items-start">
						<span class=" w-24 flex-shrink-0">Tag:</span>
						<span class="">{sourceInfo.tag}</span>
					</div>
				{/if}
			</div>
		</div>

		<!-- Status Message -->
		{#if !isReady && statusText !== "Unknown"}
			<div class="flex items-start gap-2 p-3  rounded">
				<ExclamationCircleOutline
					size="sm"
					class="text-yellow-400 mt-0.5 flex-shrink-0"
				/>
				<div>
					<div class="text-sm font-medium  mb-1">Status Message</div>
					<div class="text-sm ">{statusText}</div>
				</div>
			</div>
		{/if}

		<!-- Conditions -->
		<div class="border-t  pt-4">
			<button
				class="flex items-center justify-between w-full text-sm font-semibold mb-3 hover:"
				onclick={() => (showConditions = !showConditions)}
			>
				<span>Conditions ({conditions.length})</span>
				{#if showConditions}
					<ChevronUpOutline size="sm" />
				{:else}
					<ChevronDownOutline size="sm" />
				{/if}
			</button>

			{#if showConditions && conditions.length > 0}
				<div class="space-y-2">
					{#each conditions as condition}
						<div class="text-xs p-3 rounded ">
							<div class="flex items-center justify-between mb-2">
								<span class="font-medium ">{condition.type}</span>
								<Badge
									color={condition.status === "True" ? "green" : "red"}
									class="text-xs"
								>
									{condition.status}
								</Badge>
							</div>
							{#if condition.reason}
								<div class=" mb-1">Reason: {condition.reason}</div>
							{/if}
							{#if condition.message}
								<div class=" mb-1">{condition.message}</div>
							{/if}
							{#if condition.lastTransitionTime}
								<div class="flex items-center gap-1 ">
									<ClockOutline size="xs" />
									{formatTime(condition.lastTransitionTime)}
								</div>
							{/if}
						</div>
					{/each}
				</div>
			{/if}
		</div>

		<!-- Full Manifest -->
		<div class="border-t  pt-4">
			<div class="flex items-center justify-between mb-3">
				<span class="text-sm font-semibold">Full Resource Manifest</span>			
			</div>
			<pre
				class=" p-4 rounded text-xs overflow-auto max-h-96 "><code>{JSON.stringify(
					resource,
					null,
					2,
				)}</code></pre>
		</div>
	</div>
</Modal>
