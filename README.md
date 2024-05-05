# Polkadot-Rust-Substrate-Project

## Overview

- **Building a blockchain:** Simulation of a transaction on Substrate node using front end
- **Simulating a substrate network:** Simulation a substrate network by with two nodes
- **Adding trusted nodes to a network:** Simulation of a permissioned Substrate network with key authorized nodes
- **Smart contracts:** Building a basic smart contract to run on Substrate chain.

### 1. Building a blockchain
1. Cloned the the substrate node template to start a substrate node from `https://github.com/substrate-developer-hub/substrate-node-template`
2. Started a new branch with `git switch -c my-new-branch`
3. Compiled with `cargo build --release` and started the node with `./target/release/node-template --dev`
4. Cloned the template for front end from `https://github.com/substrate-developer-hub/substrate-front-end-template`
5. Installed the front end using `yarn install` and started it with `yarn start`
6. Front end available at `http://localhost:8000/substrate-front-end-template` to make a transaction

Transaction simulated here is from Alice to Dave and the amount is 1 unit.
<img width="709" alt="polkadot1" src="https://github.com/akkayadila/-Polkadot-Rust-Substrate-Project/assets/133990573/f8968183-d7c9-4e1c-9d6b-81edd54a7a1b">
<img width="454" alt="polkadot2" src="https://github.com/akkayadila/-Polkadot-Rust-Substrate-Project/assets/133990573/9b3575e1-b1db-4b1c-a5c3-91d8e0c9f103">

### 2. Simulating a substrate network
1. Purged the old chain data for Alice and Bob and starting new nodes
2. Observed the nodes discovering each other on the network and starting consensus mechanism

<img width="800" alt="polkadot7" src="https://github.com/akkayadila/-Polkadot-Rust-Substrate-Project/assets/133990573/ac687586-eb95-4032-a2b2-f15408a8a7a3">

Shown above is Alice node discovering Bob node and switching from 0 peers to 1 peer and Bob node producing a block that is imported to Alice node.

### 3. Adding trusted nodes to a network
1. Opened up two terminals with different sudo users.
2. Cloned and ran substrate node template on both terminals.
3. Generated aura key using Sr25519 signature and grandpa key using Ed25519 signature
  - Command used to get aura keys for respective users: `./target/release/node-template key generate --scheme Sr25519 --password-interactive`
  - Command used to get grandpa key for the first user: `./target/release/node-template key inspect --password-interactive --scheme Ed25519 "clay also afford asthma burden swallow enable rebuild duck range champion habit"`
  - Command used to get grandpa key for the first user: `./target/release/node-template key inspect --password-interactive --scheme Ed25519 "digital lunar lyrics brush witness funny rebuild master love stable hair gas"`
4. Exported the local chain specifications into `customSpec.json` using the command `./target/release/node-template build-spec --disable-default-bootnode --chain local > customSpec.json`
5. Opened `customSpec.json` and edited in aura and granpa keys generated earlier to obtain custom chain specifications with validators.
6. Converted .json file to raw format with command `./target/release/node-template build-spec --chain=customSpec.json --raw --disable-default-bootnode > customSpecRaw.json` so that the chain can be used
7. Ran the first node using the command:
  ```
  ./target/release/node-template --base-path/tmp/node01 --chain ./customSpecRaw.json --port 30333 --rpc-port 9933 --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" --validator --rpc-methods Unsafe --name MyNode01 --password-interactive
  ```
8. Purged node1 and node2 using command `./target/release/node-template purge-chain --base-path /tmp/node<node-number> --chain local`
9. Stored aura and grandpa keys of both nodes in the keystore
10. Checked if keys are stored using command `ls /tmp/node<node-number>/chains/local_testnet/keystore`

    Got outputs confirming both keys are stored in the keystore of each node
    ```
    6175726190ab52dea3e6f3c4fa4e63c27c198996f630e3bfa5bdc93a7b4ef94233df2118
    6772616e8839da2c1d94331a28d198f8927247f661a68a1a477858dbf9aae83d30989353
    ```
    ```
    61757261d8f536e52327b37dc69323151ad06e9235801c763275511c3bbc0e261f7baa5f
    6772616e9f33e11da27a45c110e7b3bac0af16a917cf4b5ab1769b05f44dd2a00f5f4288
    ```
11. Ran the first node again using the command on step 7 and ran the second node using command
```
./target/release/node-template --base-path /tmp/node02 --chain ./customSpecRaw.json --port 30334 --rpc-port 9934 --telemetry-url "wss://telemetry.polkadot.io/submit/ 0" --validator --rpc-methods Unsafe --name MyNode02 --bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWLmrYDLoNTyTYtRdDyZLWDe1paxzxTw5RgjmHLfzW96SX --password-interactive
```
12. Established connection
```
2023-09-15 13:32:43 discovered: 12D3KooWCKcvZrtEtAU7TdYvRsnJEUJo1fVqEJaYr9gZSGLbTJnD /ip4/172.29.95.146/tcp/30333
2023-09-15 13:32:48 ✨ Imported #1 (0x1124…d394)
2023-09-15 13:32:48 💤 Idle (1 peers), best: #1 (0x1124…d394), finalized #0 (0x6b8e…2bce), ⬇ 0.9kiB/s ⬆ 0.9kiB/s
```

<img width="960" alt="node1 init" src="https://github.com/akkayadila/-Polkadot-Rust-Substrate-Project/assets/133990573/3cbe801a-3c7d-4555-b31b-9c09df0e7b26">

Starting the first node 

<img width="960" alt="node2 init" src="https://github.com/akkayadila/-Polkadot-Rust-Substrate-Project/assets/133990573/6da1b82d-3f76-40b1-b82e-3e5c67038fa5">

Starting the second node 


<img width="960" alt="part 3 connected" src="https://github.com/akkayadila/-Polkadot-Rust-Substrate-Project/assets/133990573/7000badd-a477-45f0-bb44-a63fb1aee1f3">

Nodes established connection and started producing blocks



### 8. Smart contracts (added 21.09.2023)
> [!NOTE]
> In this section, contract building was having errors while loading wasm in the current rustup toolchain v1.72.0 with the following error log 
>
>```
>dila@DESKTOP-VNOUVCJ:~/flipper$ cargo contract build
> [1/5] Building cargo project
>    Updating crates.io index
>    Finished release [optimized] target(s) in 4.63s
> [2/5] Post processing wasm file
>ERROR: Loading of original wasm failed
>```
>
>so I switched to v1.69.0 while installing the CLI tool, which fixed the problem

1. Updated rustup tool with the command `rustup component add rust-src --toolchain 1.69`
2. A CLI (command-line interface) tool is needed to compile and deploy smart contracts. Installed cargo contract CLI tool with the command `cargo install --force --locked cargo-contract --version 2.0.0-rc`
3. Installed rust wasm toolchain with the command `rustup target add wasm32-unknown-unknown --toolchain 1.69`
4. Installed Substrate Contracts Node, a blockchain that includes smart contract functionality, with the command `cargo install contracts-node --git https://github.com/paritytech/substrate-contracts-node.git --tag v0.31.0 --force --locked`
5. Switched to substrate-node-template folder and created a new branch with `git switch -c my-learning-branch` and compiled with `cargo build --release`
6. Created a folder named 'flipper' using the command `cargo contract new flipper`
7. Changed to the new folder with `cd flipper` and executed the tests for the flipper contract by running `cargo test`

Got the output saying both tests from lib.rs file run successfully

```
running 2 tests
test flipper::tests::it_works ... ok
test flipper::tests::default_works ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.00s
```

8. Compiled the contract with `cargo contract build`

```
Original wasm size: 36.4K, Optimized: 12.4K

The contract was built in DEBUG mode.

Your contract artifacts are ready. You can find them in:
/home/dila/flipper/target/ink

  - flipper.contract (code + metadata)
  - flipper.wasm (the contract's code)
  - metadata.json (the contract's metadata)
```

9. Started the local node using the command `substrate-contracts-node --log info,runtime::contracts=debug 2>&1`
10. Opened a new terminal and switched to flipper folder and instantiated the flipper contract with the command `cargo contract instantiate --constructor new --args "false" --suri //Alice --salt $(date +%s)`

```
    Event System ➜ ExtrinsicSuccess
    dispatch_info: DispatchInfo { weight: Weight { ref_time: 3596541484, proof_size: 9095 }, class: Normal, pays_fee: Yes }
```

11. Noted the contract address in the following output to be able to call the contract

```
    Event Contracts ➜ Instantiated
    deployer: 5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY
    contract: 5DkKMp4RUMqUiw8Fa4PFfb4ZdfRurqZ8dJtf54GMLBZ5qWHz
```

12. Called the `get()` message using the command below after replacing the `$INSTANTIATED_CONTRACT_ADDRESS` variable with the actual adress in previous step

`cargo contract call --contract $INSTANTIATED_CONTRACT_ADDRESS --message get --suri //Alice --dry-run`

Following output is obtained, with the bool value being `false`

```
    Result Success!
    Reverted false
    Data Tuple(Tuple { ident: Some("Ok"), values: [Bool(false)] })
```
13. In order to switch the bool value from `false` to `true`, we use `flip()` message, with `$INSTANTIATED_CONTRACT_ADDRESS` replaced again with the actual adress

```
cargo contract call --contract $INSTANTIATED_CONTRACT_ADDRESS --message flip --suri //Alice
```
14. Called the `get` message again as we did in step 12, and obtained the following output
```
    Result Success!
    Reverted false
    Data Tuple(Tuple { ident: Some("Ok"), values: [Bool(true)] })
```
As we can see, the bool value is now `true`

<img width="631" alt="8" src="https://github.com/akkayadila/-Polkadot-Rust-Substrate-Project/assets/133990573/1debf25e-d6a1-4997-890b-6a5f105b4c82">

Using get() and flip() messages to interact with the contract
