<script lang="ts">
	import { page } from '$app/stores';
	import axios from 'axios';
	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';

	let dataSensor = {
		pm10: '',
		pm25: '',
		so2: '',
		co: '',
		o3: '',
		no2: '',
		max: '',
		critical: '',
		category: '',
		location: ''
	};

	const { id } = $page.params;

	onMount(async () => {
		try {
			const response = await axios.get(`https://backend-elysiajs.up.railway.app/sensor/${id}`);
			dataSensor = {
				pm10: response.data.pm10,
				pm25: response.data.pm25,
				so2: response.data.so2,
				co: response.data.co,
				o3: response.data.o3,
				no2: response.data.no2,
				max: response.data.max,
				critical: response.data.critical,
				category: response.data.category,
				location: response.data.location
			};
		} catch (error) {
			console.error('Error fetching sensor data:', error);
		}
	});

	let isSubmitting = false;
	let buttonText = 'Update Data';

	// Function to handle form submission
	const handleSubmit = async (e: Event) => {
		e.preventDefault();
		isSubmitting = true;
		buttonText = 'Submitting...';

		try {
			const response = await fetch(`http://localhost:3000/sensor/${id}`, {
				method: 'PATCH',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify(dataSensor)
			});

			if (!response.ok) {
				throw new Error(`HTTP error! status: ${response.status}`);
			}

			const result = await response.json();
			console.log('Data successfully updated:', result);
			dataSensor = {
				pm10: '',
				pm25: '',
				so2: '',
				co: '',
				o3: '',
				no2: '',
				max: '',
				critical: '',
				category: '',
				location: ''
			};
			goto('/dashboard');
		} catch (error) {
			console.error('Error updating data:', error);
		} finally {
			isSubmitting = false;
			buttonText = 'Update Data';
		}
	};
</script>

<div
	class="mx-auto mb-5 mt-5 max-w-2xl rounded-md border border-gray-600 p-8 shadow-sm shadow-slate-500"
>
	<h1 class="mb-6 text-2xl font-bold">Update Sensor Data</h1>
	<form class="space-y-6" on:submit|preventDefault={handleSubmit}>
		<!-- Form Fields -->
		<div>
			<label class="mb-1 block text-sm font-medium" for="pm10">Particulate Matter (PM10)</label>
			<input
				id="pm10"
				type="number"
				bind:value={dataSensor.pm10}
				placeholder="Enter data pm10"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="pm25">Particulate Matter (PM2.5)</label>
			<input
				id="pm25"
				type="number"
				bind:value={dataSensor.pm25}
				placeholder="Enter data pm2.5"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="so2">Sulfur Dioksida (SO2)</label>
			<input
				id="so2"
				type="number"
				bind:value={dataSensor.so2}
				placeholder="Enter data so2"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="co">Karbon Monoksida (CO)</label>
			<input
				id="co"
				type="number"
				bind:value={dataSensor.co}
				placeholder="Enter data co"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="o3">Ozon (O3)</label>
			<input
				id="o3"
				type="number"
				bind:value={dataSensor.o3}
				placeholder="Enter data O3"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="no2">Nitrogen Dioksida (NO2)</label>
			<input
				id="no2"
				type="number"
				bind:value={dataSensor.no2}
				placeholder="Enter data no2"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="max">Maksimal (Max)</label>
			<input
				id="max"
				type="number"
				bind:value={dataSensor.max}
				placeholder="Enter data max"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="critical">Critical</label>
			<input
				id="critical"
				type="text"
				bind:value={dataSensor.critical}
				placeholder="Enter data critical"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="category">Category</label>
			<input
				id="category"
				type="text"
				bind:value={dataSensor.category}
				placeholder="Enter data category"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<div>
			<label class="mb-1 block text-sm font-medium" for="location">Location</label>
			<input
				id="location"
				type="text"
				bind:value={dataSensor.location}
				placeholder="Enter data Location"
				class="mx-auto w-full rounded-lg border px-4 py-2 shadow-sm"
				required
			/>
		</div>

		<button
			type="submit"
			class="w-full rounded-lg bg-blue-600 py-2 font-semibold text-white transition hover:bg-blue-700"
			disabled={isSubmitting}
		>
			{buttonText}
		</button>
	</form>
</div>
