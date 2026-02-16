<script lang="ts">
	import Icon from '@iconify/svelte';
	import { store, apiUrl } from '$lib/store';

	$store;
	$apiUrl;

	let loading: boolean = false;
	const showSpinner = () => {
        console.log(".")
		loading = true;
		setTimeout(() => (loading = false), 250);
	};
</script>

<div class="w-full pt-12">
	<div class="flex justify-center">
		<div>API URL</div>
	</div>
	<div class="grid w-full grid-cols-7">
		<div class="col-span-3 col-start-3 flex p-1">
			<div class="input-group input-group-divider grid-cols-[auto_1fr_auto]">
				<div class="input-group-shim">
					{#if loading}
						<Icon icon="mdi:timer-sand" />
					{:else}
						<Icon icon="mdi:check-bold" />
					{/if}
				</div>
				<input
					type="text"
					placeholder="http://localhost:8080"
					bind:value={$apiUrl}
					on:keyup={showSpinner}
					class="input m-4 p-4"
				/>
			</div>
		</div>
	</div>

	<div class="mt-8 flex justify-center">
		<div>API Key</div>
	</div>
	<div class="grid w-full grid-cols-7">
		<div class="col-span-3 col-start-3 flex p-1">
			<div class="input-group input-group-divider grid-cols-[auto_1fr_auto]">
				<div class="input-group-shim">
					{#if loading}
						<Icon icon="mdi:timer-sand" />
					{:else}
						<Icon icon="mdi:check-bold" />
					{/if}
				</div>
				<input
					type="text"
					placeholder="api key"
					bind:value={$store}
					on:keyup={showSpinner}
					class="input m-4 p-4"
				/>
			</div>
		</div>
	</div>
</div>
