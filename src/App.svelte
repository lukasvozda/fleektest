<script>
  import logo from './assets/svelte.png'
  import Counter from './lib/Counter.svelte'
  import { Actor, HttpAgent } from "@dfinity/agent";
  import { getAccountIdentifier } from "./utils/utils";
  import nft_idl from "./utils/nft.did";
  import { onMount } from "svelte";

  const ic_agent = new HttpAgent({ host: "https://boundary.ic0.app/" });
    let readActorCache = {}

    let tokens = 0;
    var loading = false;

    var transactions = []

    function getReadActor(cid, idl) {
        if (cid in readActorCache)
            return readActorCache[cid]

        const actor = Actor.createActor(idl, {
            agent: ic_agent,
            canisterId: cid,
        });

        readActorCache[cid] = actor;

        return actor;
    }

  const getBalance = async () => {
    console.log("Get transactions")

    //const publicKey = await window.ic.plug.requestConnect();

    // const connected = await window.ic.plug.isConnected();

    // if (!connected)  {
    //     const publicKey = await window.ic.plug.requestConnect({ 
    //         //host: 'http://127.0.0.1',
    //         whitelist: ['rno2w-sqaaa-aaaaa-aaacq-cai','l53cn-iiaaa-aaaah-qcwia-cai'],
    //         timeout: 50000
    //     })
    // }
    // const principalId = await window.ic.plug.getPrincipal();

    let ledger = getReadActor('oeee4-qaaaa-aaaak-qaaeq-cai', nft_idl);
    //let account = getAccountIdentifier(principalId);

    let trans = await ledger.transactions();
    console.log(trans)
    transactions = trans;
    return trans;
}

onMount(getBalance)
</script>


<main>
  <img src={logo} alt="Svelte Logo" />
  <h1>Hello world!</h1>

  <Counter />

  <p>
    Visit <a href="https://svelte.dev">svelte.dev</a> to learn how to build Svelte
    apps.
  </p>

  <p>
    Check out <a href="https://github.com/sveltejs/kit#readme">SvelteKit</a> for
    the officially supported framework, also powered by Vite!
  </p>

  {#each transactions as t }
    <div>{t.time}, {t.price}</div>
  {/each}

</main>

<style>
  :root {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
      Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  }

  main {
    text-align: center;
    padding: 1em;
    margin: 0 auto;
  }

  img {
    height: 16rem;
    width: 16rem;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4rem;
    font-weight: 100;
    line-height: 1.1;
    margin: 2rem auto;
    max-width: 14rem;
  }

  p {
    max-width: 14rem;
    margin: 1rem auto;
    line-height: 1.35;
  }

  @media (min-width: 480px) {
    h1 {
      max-width: none;
    }

    p {
      max-width: none;
    }
  }
</style>
