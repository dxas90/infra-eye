<script lang="ts">
import ConnectionStatus from "$lib/components/ConnectionStatus.svelte"
import { signOut } from "@auth/sveltekit/client"
import { Button } from "flowbite-svelte"
import { ArrowRightToBracketOutline } from "flowbite-svelte-icons"

interface Props {
  connectionStatus: {
    status: "connecting" | "connected" | "disconnected" | "error"
    errors: (string | null)[]
  }
  session?: {
    user?: {
      name?: string | null
      email?: string | null
    }
  } | null
}

let { connectionStatus, session }: Props = $props()

async function handleLogout() {
  await signOut({ redirectTo: "/login" })
}
</script>

<header class="sticky top-0 z-50 px-6 py-5 border-b  bg-white">
  <div class="max-w-7xl mx-auto">
    <div class="flex items-center justify-between">
      <div>
        <h1 class="text-2xl font-bold bg-gradient-to-r from-blue-600 to-cyan-600 bg-clip-text text-transparent">
          Infra Eye — GitOps Dashboard
        </h1>
        <p class="text-sm  mt-1.5">
          Real-time Kubernetes Flux resources monitoring
        </p>
      </div>

      <div class="flex items-center gap-4">
        <ConnectionStatus
          status={connectionStatus.status}
          errors={connectionStatus.errors}
        />
        
        {#if session?.user}
          <div class="flex items-center gap-3">
            <div class="text-sm ">
              <div class="font-medium">{session.user.name || session.user.email}</div>
            </div>
            <Button size="xs" color="alternative" onclick={handleLogout}>
              <ArrowRightToBracketOutline size="xs" class="mr-1" />
              Logout
            </Button>
          </div>
        {/if}
      </div>
    </div>
  </div>
</header>