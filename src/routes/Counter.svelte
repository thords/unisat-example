<script>
	import { spring } from 'svelte/motion';

	let hasWallet = typeof(unisat) !== 'undefined'

	$: accounts = []
	$: connectResult = ""
	$: opResult = ""
	$: network = ""

	if( hasWallet ) {
		unisat.on(`accountsChanged` , ( values ) => {
			accounts = values 
			connectResult = accounts.length > 0 ? accounts : 'No connect.'
		})

		unisat.on(`networkChanged` , ( value ) => {
			network = value 
		})

	}
	
	async function connect(){
		connectResult = "Waiting for unisat approve."
		accounts = await unisat.requestAccounts()
		connectResult =  accounts.length > 0 ? accounts : 'No connect.'

		network = await unisat.getNetwork()

	}

	async function signMessage(){
		const v = await unisat.signMessage(`hello thords.`)
		opResult = `SignedMessage -> ${v}`
	}

	async function signPSBT(){
		const hex = ``
		opResult = `Wait for unisat signature`
		const v = await unisat.signPsbt(hex)
		opResult = `Signed success. value length is ${v.length}`
	}

</script>

<div class="message green">
	Chrome Extensions: { hasWallet?"UniSat wallet is installed":"Unisat wallet is not installed"} 
</div>

<div class="network">
	Network : { network }
</div>

<div class="accounts">
	Available Accounts: { connectResult }
</div>

<div class="opResult">
	Op Result : { opResult }
</div>

<div class="counter">
	<!-- <button on:click={() => (count -= 1)} aria-label="Decrease the counter by one">
		<svg aria-hidden="true" viewBox="0 0 1 1">
			<path d="M0,0.5 L1,0.5" />
		</svg>
	</button>

	<div class="counter-viewport">
		<div class="counter-digits" style="transform: translate(0, {100 * offset}%)">
			<strong class="hidden" aria-hidden="true">{Math.floor($displayed_count + 1)}</strong>
			<strong>{Math.floor($displayed_count)}</strong>
		</div>
	</div> -->
<!-- 
	<button on:click={ connect } aria-label="Request Accounts" disabled={!hasWallet}>
		<span> Accounts </span>
	</button> -->
	{#if accounts.length === 0 }
		<button on:click={connect} aria-label="Connect unisat wallet." disabled={!hasWallet}>
			<span> Connect </span>
		</button>
	{:else}
		<button on:click={signMessage} aria-label="Sign message." disabled={!hasWallet}>
			<span> SignMessage </span>
		</button>
		<button on:click={signPSBT} aria-label="Sign psbt." disabled={!hasWallet}>
			<span> SignPSBT </span>
		</button>
	{/if}
</div>

<style>
	.counter {
		display: flex;
		border-top: 1px solid rgba(0, 0, 0, 0.1);
		border-bottom: 1px solid rgba(0, 0, 0, 0.1);
		margin: 1rem 0;
	}

	.counter button {
		width: 7em;
		padding: 0;
		display: flex;
		align-items: center;
		justify-content: center;
		border: 0;
		background-color: transparent;
		touch-action: manipulation;
		font-size: 2rem;
	}

	.counter button:hover {
		background-color: var(--color-bg-1);
	}
</style>
