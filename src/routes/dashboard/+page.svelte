<script>
	// @ts-nocheck
	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';

	let menuItems = ['Dashboard', 'Reports', 'Settings'];
	let isSidebarOpen = false;
	let errorMessage = null;
	let sensorData = [];
	let loading = true;

	function toggleSidebar() {
		isSidebarOpen = !isSidebarOpen;
	}

	onMount(async () => {
		try {
			const response = await fetch('http://backend-elysiajs.up.railway.app/sensor/getAll');
			const data = await response.json();
			if (response.ok) {
				sensorData = data;
				loading = false;
			} else {
				errorMessage = data.message;
			}
		} catch (error) {
			console.error(error);
		}
	});

	function deleteData(id) {
		fetch(`https://backend-elysiajs.up.railway.app/sensor/${id}`, {
			method: 'DELETE'
		})
			.then((response) => {
				if (response.ok) {
					sensorData = sensorData.filter((item) => item.id !== id);
				} else {
					console.error('Error deleting data:', response.statusText);
				}
			})
			.catch((error) => {
				console.error('Error deleting data:', error);
			});
	}

	function updateData(item) {
		goto(`/sensor/update/${item.id}`);
	}
	function goToCreatePage() {
		goto('/sensor/create');
	}
</script>

<div class="flex h-screen bg-gray-100">
	<!-- Sidebar -->
	<div
		class={`flex flex-col bg-blue-600 text-white transition-all ${isSidebarOpen ? 'w-64' : 'w-16'} duration-300`}
	>
		<div class="flex items-center justify-between border-b border-blue-400 p-4 text-lg font-bold">
			<span class={`${isSidebarOpen ? 'block' : 'hidden'} duration-300`}>My Dashboard</span>
			<button
				on:click={toggleSidebar}
				class="ml-auto rounded p-2 text-white hover:bg-blue-500"
				aria-label="Toggle Sidebar"
			>
				{isSidebarOpen ? '←' : '→'}
			</button>
		</div>
		<ul class="flex-1">
			{#each menuItems as item}
				<li
					class="flex cursor-pointer items-center gap-2 p-4 transition-all duration-300 hover:bg-blue-500"
				>
					<span class="h-6 w-6 rounded-full bg-white"></span>
					<span class={`${isSidebarOpen ? 'block' : 'hidden'} duration-300`}>{item}</span>
				</li>
			{/each}
		</ul>
		<div class="border-t border-blue-400 p-4">
			<span class={`${isSidebarOpen ? 'block' : 'hidden'} duration-300`}>Logout</span>
		</div>
	</div>

	<!-- Main Content -->
	<div class="flex-1 p-6">
		<h1 class="text-2xl font-semibold text-gray-800">Welcome to the Dashboard!</h1>

		<!-- Button to navigate to create sensor page -->
		<button
			type="button"
			class="mx-auto mb-5 mt-5 rounded-md bg-blue-400 px-3 py-2 text-black"
			on:click={goToCreatePage}
		>
			Create New Sensor Data
		</button>

		{#if errorMessage}
			<p class="mt-4 text-red-500">{errorMessage}</p>
		{:else if sensorData.length === 0}
			<p class="mt-4 text-gray-500">Loading data...</p>
		{:else}
			<div class="mt-6 grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3">
				{#each sensorData as item (item.id)}
					<div class="rounded-lg bg-white p-2 shadow">
						<h2 class="text-lg font-medium text-gray-800">{item.location}</h2>
						<p class="mt-2 text-sm text-gray-600">Category: {item.category}</p>
						<p class="text-sm text-gray-600">Critical: {item.critical}</p>
						<p class="text-sm text-gray-600">PM10: {item.pm10}</p>
						<p class="text-sm text-gray-600">PM2.5: {item.pm25}</p>
						<p class="text-sm text-gray-600">SO2: {item.so2}</p>
						<p class="text-sm text-gray-600">CO: {item.co}</p>
						<p class="text-sm text-gray-600">O3: {item.o3}</p>
						<p class="text-sm text-gray-600">NO2: {item.no2}</p>
						<p class="mt-4 text-xs italic text-gray-500">
							Updated at: {new Date(item.updatedAt).toLocaleString()}
						</p>

						<!-- Action Buttons (Delete, Update) -->
						<div class="mt-4 flex gap-2">
							<button
								class="rounded bg-red-600 px-4 py-2 text-sm font-medium text-white hover:bg-red-700"
								on:click={() => deleteData(item.id)}
							>
								Delete
							</button>
							<button
								class="rounded bg-blue-600 px-4 py-2 text-sm font-medium text-white hover:bg-blue-700"
								on:click={() => updateData(item)}
							>
								Update
							</button>
						</div>
					</div>
				{/each}
			</div>
		{/if}
	</div>
</div>
