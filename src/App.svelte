<script lang="ts">
	import { children, merge_ssr_styles } from "svelte/internal";
	let input: string = "";
	let selection: string = "text";
	const ord = (x: string) => x.charCodeAt(0);

	const decode_letters = (msg) => {
		const n_letters = 26;
		// Decoding offset for the very basic crypto.
		const offset = -3;

		let output = "";
		for (let i = 0; i < msg.length; i++) {
			if (ord(msg[i]) < ord("a") || ord(msg[i]) > ord("z")) {
				output += msg[i];
				continue;
			}

			const position =
				(ord(msg[i]) - ord("a") + n_letters + offset) % n_letters;
			output += String.fromCharCode(position + ord("a"));
		}

		return output;
	};

	const decode_numbers = (message: string) => {
		const words = message.split(" ");
		let msg = "";
		for (const word of words) {
			if (word == "") continue;
			const letters = word.split("-").map(x => {
				// Magic number to return a space if no char is detected.
				const n = x ? Number.parseInt(x) : -64;
				return String.fromCharCode(ord('a') + n - 1)
			}).join("")
			msg += letters + " ";
		}
		
		return msg;
	};

	const decode = (msg, value) => {
		switch (value) {
			case "text":
				return decode_letters(msg);
			case "number":
				return decode_numbers(msg);
				break;
			default:
				throw "Shouldn't reach";
		}
	};

	$: output = decode(input, selection);
</script>

<main>
	<img src="https://upload.wikimedia.org/wikipedia/sco/c/c2/Gravity_Falls_logo.png" width="100px" alt="">
	<p>decoder</p>
	<select
		name="decode_kind"
		bind:value={selection}
		on:change={(value) => decode(input, selection)}
	>
		<option value="text" default>Texto</option>
		<option value="number">Números</option>
	</select>

	<textarea bind:value={input} placeholder="Enter the message" rows="20" />

	<textarea rows="3">{output}</textarea>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 500px;
		margin: 0 auto;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
