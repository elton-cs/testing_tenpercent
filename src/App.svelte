<script lang="ts">
  import { RpcProvider, Contract, AccountInterface, Account } from "starknet";
  import { onMount } from "svelte";
  import Controller, { type SessionPolicies } from "@cartridge/controller";

  const rpcUrl = "https://api.cartridge.gg/x/tenpercent/katana";
  const providerKatanaDev = new RpcProvider({
    nodeUrl: rpcUrl,
  });

  //     | Account address |  0x2af9427c5a277474c079a1283c880ee8a6f0f8fbf73ce969c08d88befec1bba
  // | Private key     |  0x1800000000300000180000000000030000000000003006001800006600
  // | Public key      |  0x2b191c2f3ecf685a91af7cf72a43e7b90e2e41220175de5c4f7498981b10053

  const accountAddress =
    "0x2af9427c5a277474c079a1283c880ee8a6f0f8fbf73ce969c08d88befec1bba";
  const privateKey =
    "0x1800000000300000180000000000030000000000003006001800006600";
  const account = new Account(providerKatanaDev, accountAddress, privateKey);

  const mainContractAddr =
    "0x5fe3cd9b8a92c16fee81e08579cc87c1328aa9a375b7d1f472ee183af4e12b8";
  let mainContractClass: any;
  let mainContract: Contract;

  onMount(async () => {
    mainContractClass = await providerKatanaDev.getClassAt(mainContractAddr);
    mainContract = new Contract(
      mainContractClass.abi,
      mainContractAddr,
      account,
    );
    mainContract.connect(account);
    console.log("Account connected to contract");
  });
  let updatedPoints: bigint = $state(0n);

  async function updatePoints() {
    const points: bigint = await mainContract.get_points();
    updatedPoints = points;
  }

  async function startGame() {
    const tx = await mainContract.start_game();
    console.log(tx);
  }

  async function resetGame() {
    const tx = await mainContract.reset_game();
    console.log(tx);
  }
</script>

<main>
  <button onclick={updatePoints}>Update Points</button>
  <p>Points: {updatedPoints}</p>
  <button onclick={startGame}>Start Game</button>
  <button onclick={resetGame}>Reset Game</button>
</main>
