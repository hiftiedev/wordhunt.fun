<script context="module">
	import words from '../words.json';
</script>

<script lang="ts">
	import { onMount, onDestroy } from 'svelte';

	let randomString: string = '';
	let message: string = 'Press Enter to start or refresh';
	let userInput: string = '';
	let resultMessage: string = '';

	function generateRandomStringWithWord(length: number): string {
		const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';

		let result = '';
		let addedWord = false;

		const wordPosition = Math.floor(Math.random() * (length / 5 + 1)) * 5;

		for (let i = 0; i < length; i++) {
			if (!addedWord && i === wordPosition) {
				const randomWordIndex = Math.floor(Math.random() * words.length);
				const randomWord = words[randomWordIndex];

				const randomCaseWord = randomWord.charAt(0).toUpperCase() + randomWord.slice(1).toLowerCase();

				result += randomCaseWord;
				addedWord = true;

				localStorage.setItem('hiddenWord', randomWord.toLowerCase());
			} else {
				const randomIndex = Math.floor(Math.random() * characters.length);
				result += characters.charAt(randomIndex);
			}
		}

		return result;
	}

	onMount(() => {

		localStorage.removeItem('hiddenWord');
		document.addEventListener('keydown', handleKeyDown);
	});

	onDestroy(() => {
		localStorage.removeItem('hiddenWord');
	});

	function handleKeyDown(event: KeyboardEvent) {
		if (event.key === 'Enter') {
			randomString = generateRandomStringWithWord(60);
			message = '';
			userInput = '';
			resultMessage = '';
		}
	}

	function checkUserInput() {
		const trimmedUserInput = userInput.trim().toLowerCase();
		const hiddenWord = localStorage.getItem('hiddenWord');

		if (trimmedUserInput === hiddenWord) {
			resultMessage = 'Correct!';
		} else {
			resultMessage = 'Wrong!';
		}

		localStorage.removeItem('hiddenWord');

		randomString = generateRandomStringWithWord(60);
	}
</script>

<svelte:head>
	<title>WordHunt</title>
	<meta name="description" content="Challenge your eyes and hunt for words within sentences that do not contain any spaces between them." />
</svelte:head>

<main class="flex items-center justify-center h-screen">
	<div class="bg-[#242632] w-full sm:max-w-[80%] md:max-w-[60%] lg:max-w-[40%] p-4 text-white overflow-hidden">
		{#if message}
			<div class="break-words text-center">{message}</div>
		{:else}
			<div class="break-words text-center">{randomString}</div>
			<input class="text-black" type="text" bind:value={userInput} placeholder="Type the hidden word" />
			<button on:click={checkUserInput}>Check</button>
			{#if resultMessage}
				<p class="{resultMessage === 'Correct!' ? 'text-green-500' : 'text-red-500'}">{resultMessage}</p>
			{/if}
		{/if}
	</div>
</main>

<style>
</style>
