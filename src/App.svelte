<script>
  import logo from './assets/svelte.png'
  import Counter from './lib/Counter.svelte'
  import { Actor, HttpAgent } from "@dfinity/agent";
  import { getAccountIdentifier } from "./utils/utils";
  import nft_idl from "./utils/nft.did";
  import { onMount } from "svelte";
  import { canisters } from "./utils/canisters.js"

  const ic_agent = new HttpAgent({ host: "https://boundary.ic0.app/" });
  let readActorCache = {}

  let tokens = 0;
  var loading = false;

  var transactions = []

  let transCount = 0;

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
    console.log(canisters)
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


//     let ledger = getReadActor("oeee4-qaaaa-aaaak-qaaeq-cai", nft_idl);
//       //let account = getAccountIdentifier(principalId);

//       let trans = await ledger.transactions();
//       console.log(trans)
//       transactions = trans;
//       //transactions = transactions.push(trans)
//       //console.log(transactions)
//     return trans;
// }

    const getTransactions = async (canister) => {
      console.log("Getting data for canister: " + canister)
      let ledger = getReadActor(canister, nft_idl);


      let trans = await ledger.transactions();
      //console.log(trans)

      transactions = transactions.concat(trans)
      transCount = transactions.length
      console.log("Canister: " + canister + " " + "done!")

    }

    for (var c in canisters){
      // console.log("Getting data for canister: " + canisters[c].name + " " + canisters[c].id)
      // let ledger = getReadActor(canisters[c].id, nft_idl);

      getTransactions(canisters[c].id)
      // let trans = await ledger.transactions();
      // console.log(trans)

      // transactions = transactions.concat(trans)

    }
    //console.log(transactions)
    return true;
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
  <h1># {transCount}</h1>

  {#each transactions as t, index }
    <div>{index}, {t.time}, {t.price}</div>
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
