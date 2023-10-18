<script>
	import { onMount } from 'svelte';

	//on load connect to web scoket
	//on load get data from web socket
	// @ts-ignore
	let data = [];
	let isReload = false;
    // @ts-ignore
    let socket;

    let currentMessage = '';

	onMount(async () => {
		socket = new WebSocket('ws://chat-api.cwute.dev/chat');
		socket.onopen = () => {
			// @ts-ignore
			socket.send('Connected');
			isReload = true;
		};
		socket.onmessage = (event) => {
			let message = JSON.parse(event.data);
			// @ts-ignore
			let messages = [...data, message];
			data = messages;
		};

        socket.onclose = () => {
            alert('Connection closed. Prolly said python or smth offensive');
        };
	});

	function sendMessage(){
        // @ts-ignore
        socket.send(currentMessage);
        currentMessage = '';
    }
</script>

{#if isReload}
	<div class="grid grid-rows-6 grid-cols-1 gap-10 p-10 scroll-smooth" style="position: relative;">
		<div class=" row-span-5 overflow-x-auto h-[450px] ">
			{#each data as item}
				<div class="border-4 p-5 m-5 border-orange-500">
					<p class="font-bold">{item.sender}:</p>
					<p>{item.message}</p>
				</div>
			{/each}
		</div>
		<div class="row-span-1 col-span-1 h-16">
			<!-- input area with send button-->
			<div class="flex">
				<input
					type="text"
					class="border-2 border-orange-500 rounded-l-lg p-2 w-full"
					placeholder="Type your message here..."
                    bind:value={currentMessage}
				/>
				<button
                    on:click={sendMessage}
					class="bg-orange-500 hover:bg-orange-700 text-white font-bold py-2 px-4 rounded-r-lg"
					>Send</button
				>
			</div>
		</div>
	</div>
{:else}
	<h1>Reloading</h1>
{/if}
