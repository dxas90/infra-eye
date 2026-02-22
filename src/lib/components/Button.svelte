<script lang="ts">
import { Button as FBButton } from "flowbite-svelte"
import type { HTMLButtonAttributes } from "svelte/elements"

export let variant: "primary" | "secondary" | "ghost" | "danger" = "primary"
export let size: "xs" | "sm" | "md" | "lg" = "md"
export let fullWidth: boolean = false
export let loading: boolean = false
export let disabled: boolean = false
export let className: string = ""

// Map local variants to Flowbite colors/props
const color =
  variant === "primary"
    ? "primary"
    : variant === "secondary"
      ? "secondary"
      : variant === "danger"
        ? "red"
        : "alternative"
</script>

<FBButton
  color={color}
  size={size}
  loading={loading}
  disabled={disabled}
  class={(fullWidth ? 'w-full ' : '') + className}
  {...$$restProps}
>
  <slot />
</FBButton>
