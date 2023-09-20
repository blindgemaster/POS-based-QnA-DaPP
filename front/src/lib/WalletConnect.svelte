<script>
    import { ethers } from 'ethers';
	import { onMount } from 'svelte';

	let web3Props = { provider: null, signer: null, account: null, chainId: null };

	async function connectWallet() {
		if (typeof window.ethereum === 'undefined') {
			// Metamask is not installed or not accessible
			console.error('Metamask is not installed or not accessible');
			return;
		}

		try {
			await window.ethereum.request({ method: 'eth_requestAccounts' });
			const provider = new ethers.providers.Web3Provider(window.ethereum);
			const signer = provider.getSigner();
			const account = await signer.getAddress();
			const chainId = await provider.getNetwork().then((network) => network.chainId);

			web3Props = { provider, signer, account, chainId };
		} catch (error) {
			console.error('Failed to connect to Metamask:', error);
		}
	}

	// Check if Metamask is already connected on component mount
	onMount(async () => {
		if (typeof window.ethereum !== 'undefined' && window.ethereum.selectedAddress) {
			await connectWallet();
		}
	});
</script>

<button on:click={connectWallet}>Attach Wallet</button>
