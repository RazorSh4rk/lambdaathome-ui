<script lang="ts">
	import { onMount } from 'svelte';
	import { store } from '$lib/store';
	import Icon from '@iconify/svelte';

	let runtimes: string[] = [];
	let selected: string = '';
	let selectedContent: string = '';

	let newName: string = '';
	let newFile: FileList;
	let message: string = '';

	const opts = {
		headers: {
			Authorization: $store || ''
		}
	};

	const loadRuntimes = () => {
		fetch('http://localhost:8080/runtime/list', opts)
			.then((res) => res.json())
			.then((res) => {
				runtimes = res;
			})
			.catch((err) => console.error(err));
	};

	const showRuntime = (name: string) => {
		selected = name;
		fetch(`http://localhost:8080/runtime/show/${name}`, opts)
			.then((res) => res.json())
			.then((res) => {
				selectedContent = res.content || '';
			})
			.catch((err) => console.error(err));
	};

	const deleteRuntime = (name: string) => {
		fetch(`http://localhost:8080/runtime/delete/${name}`, {
			method: 'DELETE',
			headers: { Authorization: $store || '' }
		})
			.then((res) => res.json())
			.then((res) => {
				if (res.error) {
					message = res.error;
				} else {
					selected = '';
					selectedContent = '';
					loadRuntimes();
				}
			})
			.catch((err) => console.error(err));
	};

	const uploadRuntime = () => {
		if (!newName || !newFile?.length) {
			message = 'Please provide a name and Dockerfile';
			return;
		}

		const formData = new FormData();
		formData.append('file', newFile[0]);

		fetch(`http://localhost:8080/runtime/upload/${newName}`, {
			method: 'POST',
			headers: { Authorization: $store || '' },
			body: formData
		})
			.then((res) => res.json())
			.then((res) => {
				message = `Runtime '${res.runtimeName}' created`;
				newName = '';
				loadRuntimes();
			})
			.catch((err) => {
				message = `Error: ${err}`;
			});
	};

	onMount(loadRuntimes);
</script>

<div class="grid grid-cols-5 pl-2">
	<div class="flex flex-col justify-center">Runtimes</div>
	<div class="col-span-4 col-start-2 flex flex-wrap justify-center p-4 border-b">
		{#each runtimes as rt}
			<!-- svelte-ignore a11y-click-events-have-key-events -->
			<!-- svelte-ignore a11y-no-static-element-interactions -->
			<div
				class="badge variant-filled m-1 cursor-pointer"
				class:variant-ghost={selected === rt}
				on:click={() => showRuntime(rt)}
			>
				{rt}
			</div>
		{:else}
			<p class="p-4">No runtimes installed</p>
		{/each}
	</div>

	{#if selected}
		<div class="flex flex-col justify-center">Dockerfile</div>
		<div class="col-span-4 col-start-2 p-4 border-b">
			<div class="card variant-ghost p-4">
				<p class="h4"><Icon icon="mdi:docker" class="inline-block" /> {selected}</p>
				<pre class="pre mt-2 p-4 text-sm">{selectedContent}</pre>
				<div class="pt-4">
					<button
						class="btn variant-filled-error"
						on:click={() => deleteRuntime(selected)}
					>
						<Icon icon="mdi:trash-can" class="inline-block" />
						<span>Delete</span>
					</button>
				</div>
			</div>
		</div>
	{/if}

	<div class="flex flex-col justify-center">New Runtime</div>
	<div class="col-span-4 col-start-2 p-4 border-b">
		<div class="flex flex-col gap-4">
			<label class="label">
				<span>Name</span>
				<input class="input p-2" type="text" placeholder="python-3" bind:value={newName} />
			</label>

			<label class="label">
				<span>Dockerfile</span>
				<input class="input p-2" type="file" bind:files={newFile} />
			</label>

			<button class="btn variant-filled w-fit" on:click={uploadRuntime}>
				<Icon icon="mdi:plus" class="inline-block" />
				<span>Create Runtime</span>
			</button>

			{#if message}
				<div class="variant-ghost badge p-2">{message}</div>
			{/if}
		</div>
	</div>
</div>
