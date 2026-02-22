<script lang="ts">
import type { HTMLAttributes } from "svelte/elements"

interface Props {
  variant?: "default" | "elevated" | "bordered" | "interactive"
  padding?: "none" | "sm" | "md" | "lg"
  clickable?: boolean
  onclick?: () => void
  class?: string
  children?: any
}

let {
  variant = "default",
  padding = "md",
  clickable = false,
  onclick,
  class: className = "",
  children
}: Props = $props()

const baseClasses = "rounded-lg transition-all duration-200"

const variantClasses = {
  default: " border ",
  elevated: " shadow-lg shadow-slate-950/50",
  bordered: "/50 border-2 ",
  interactive:
    " border  hover: hover:shadow-lg hover:shadow-blue-500/10 cursor-pointer active:scale-[0.98]"
}

const paddingClasses = {
  none: "",
  sm: "p-3",
  md: "p-4",
  lg: "p-6"
}

const interactiveClasses = $derived(
  clickable ? "cursor-pointer hover: active:scale-[0.98]" : ""
)
</script>

{#if onclick}
	<button
		type="button"
		onclick={onclick}
		class="{baseClasses} {variantClasses[variant]} {paddingClasses[padding]} {interactiveClasses} {className} text-left w-full"
	>
		{@render children?.()}
	</button>
{:else}
	<div
		class="{baseClasses} {variantClasses[variant]} {paddingClasses[padding]} {interactiveClasses} {className}"
	>
		{@render children?.()}
	</div>
{/if}
