<script>
	import axios from 'axios';
	import { onMount } from 'svelte';

	let message = '';
	let allMessages = [];

	let socket = new WebSocket(`ws://${window.location.hostname}:8080/messages`);
	socket.onmessage = (e) => {allMessages = JSON.parse(e.data)}

	const sendMessage = () => {
		if (message) {
			socket.send(message);
			message = '';
		}
	}

	const handleEnter = (e) => {
		if (e.key === 'Enter' && message) {
			socket.send(message);
			message = '';
		}
	}

	onMount(() => {
		axios.get('/getLastMessages').then(res => {allMessages = res.data});
	});

</script>

<div class='main'>
	<div class='messages'>
		{#each allMessages as msg}
			<div>{msg}</div>
		{/each}
	</div>
	<div class='input_wrapper'>
		<input type="text" class='input' bind:value={message} on:keypress={handleEnter}>
		<div class='send_message'>
			<span on:click={sendMessage}>Send</span>
		</div>
	</div>
</div>

<style lang="scss">
	.main {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
	}

	.messages {
		flex-grow: 1;
	}

	.input_wrapper {
		width: 100%;
		height: 40px;
		position: relative;
		display: flex;

		& .input {
			width: 100%;
			height: 100%;
			padding: 10px;
			font-size: 20px;
			border-radius: 0;
			flex-grow: 1;
			border: 4px solid black;
		}

		& .send_message {
			cursor: pointer;
			flex-shrink: 0;
			width: 100px;
			height: 100%;
			background-color: black;
			display: flex;
			justify-content: center;
			align-items: center;
			font-size: 20px;
			color: white;
			font-weight: 400;
		}
	}
</style>