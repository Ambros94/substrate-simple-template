# How to start a Polkadot Chain with NPoS

First, install the rust toolchain following the instructions here
More information available here https://docs.substrate.io/v3/getting-started/installation/ 

Build subkey, the required tool to prepare the test net by following instructions here https://docs.substrate.io/v3/tools/subkey/

```bash
git clone https://github.com/Ambros94/substrate-simple-template.git 
cd substrate-simple-template
cargo build --release
```

If it gives an error related to parity-db version run: `cargo update -p parity-db` and then build again

Generate the spec 
```bash
./target/release/substrate build-spec --disable-default-bootnode --chain staging > customSpec.json
````

**Create some accounts through Polkadot.js browser extension and add them to balances in customSpec.json. The section is around line 38**

Launch the first node to get the node id
```
./target/release/substrate --tmp --dev --node-key 0x74a8cfbadb5d2b0178ec124791bfa8346ac3550a4f689923c806428090055277
```

Copy the field "Local node identity". It should look like this '12D3KooWRm651Kd5GmsLTHJbgX5chQS5npx9ttLgo46UsegCMoNM'

You can now kill the node

Change “BootNodes”: [] section to below string in customSpec.json.
to "BootNodes": ["/ip4/127.0.0.1/tcp/30333/p2p/<Your Newly Printed Local Node Identity>"]

Convert it to a raw spec and then launch the nodes 
```bash 
./target/release/substrate build-spec --chain ./customSpec.json --raw > UZHNet.json
```


Launch the first validator node, which is also the boot node for the other two
```bash
./target/release/substrate --chain UZHNet.json -d data/validator1 --name validator1 --in-peers 256 --validator --ws-external --rpc-external --rpc-cors all --rpc-methods=unsafe --node-key 0x74a8cfbadb5d2b0178ec124791bfa8346ac3550a4f689923c806428090055277
```

Launch the second validator node
```bash
./target/release/substrate --chain UZHNet.json -d data/validator2 --name validator2 --validator --port 30334 --ws-port 9946 --rpc-port 9934 --ws-external --rpc-external --rpc-cors all --rpc-methods=unsafe
```

Launch the third validator node
```bash
./target/release/substrate --chain UZHNet.json -d data/validator3 --name validator3 --validator --port 30335 --ws-port 9947 --rpc-port 9935 --ws-external --rpc-external --rpc-cors all --rpc-methods=unsafe
``` 


When the nodes are running load the keys they need to finalise blocks
```bash
cd scripts/session_keys
sh run.sh
```

To conclude, stop all the nodes and run them again using the same commands used above

You should see the chain that finalised blocks



# Install a frontend and interact with the chain

