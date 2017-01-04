# chaincode-dev-env

Chaincode (smart contract) development tool for [Hyperledger Fabric](https://github.com/hyperledger/fabric), mainly provides:

* Automatically setup a chaincode test and runtime environment.
* Scripts to deploy/invoke/query chaincodes. Meanwhile, observe logs/errors for debugging chaincodes.

*Note: Currently, it only supports Fabric v0.6*

## 1. Chaincode environment setup

Setup the chaincode development environment through Docker containers.

*This script should be triggered at the upper directory.*

```sh
$ bash scripts/setup.sh
```

## 2. Start services

Use docker-compose to start a peer and a membersrvc.

```sh
$ bash scripts/start.sh
```

## 3. Build chaincode

Put your chaincodes in `chaincodes/`. We already put two sample chaincodes there: `mycc1.go` is a copy of [chaincode_example02.go](https://github.com/hyperledger/fabric/blob/master/examples/chaincode/go/chaincode_example02/chaincode_example02.go), and `mycc2.go` is a copy of [map.go](https://github.com/hyperledger/fabric/blob/master/examples/chaincode/go/map/map.go).

Build one of your chaincodes, and register it in the chaincode environment. For example:

```sh
$ bash scripts/build_chaincode.sh mycc1
```

If build fails, you will see the error messages displayed in the console.

If build succeeds, your chaincode will be assigned a 4-digit random ID and registered in the chaincode environment:

```
Building chaincode ...
Chaincode mycc1 successfully built.
Registering chaincode using ID: 59de
03:38:29.463 [shim] DEBU : Peer address: 0.0.0.0:7051
03:38:29.464 [shim] DEBU : os.Args returns: [./mycc1]
03:38:29.466 [shim] DEBU : Registering.. sending REGISTER
03:38:29.469 [shim] DEBU : []Received message REGISTERED from shim
03:38:29.469 [shim] DEBU : []Handling ChaincodeMessage of type: REGISTERED(state:created)
03:38:29.469 [shim] DEBU : Received REGISTERED, ready for invocations
```

You can keep observing this console. It will show a lot of useful logs when testing this chaincode. Please remember the ID, which will be used to specify this chaincode when sending deploy/invoke/query requests. For the example above, the ID is `59de`.

Open a new console before continuing.

## 4. Deploy and test chaincode:

The REST interface port of the chaincode environment container is 7050. You can deploy/invoke/query chaincode via REST API, using the python scripts:

```
$ cd scripts/requests/
$ ls
deploy.py   invoke.py   query.py
```

### Deploy chaincode

Edit deploy.py, modify the chaincode ID and parameters (including the function name and params, according to the chaincode content). For example:

```python
chaincode_id = "59de"
args = ["init", "a", "100", "b", "200"]
```

Then, deploy the chaincode.

```
$ python deploy.py
```

See logs in the first console for details.

### Invoke chaincode

Similarly, edit invoke.py, modify the chaincode ID and parameters. For example:

```python
chaincode_id = "59de"
args = ["invoke", "a", "b", "5"]
```

Then, invoke the chaincode.

```
$ python invoke.py
```

See logs in the first console for details.

### Query chaincode

Similarly, edit query.py, modify the chaincode ID and parameters. For example:

```python
chaincode_id = "59de"
args = ["query", "a"]
```

Then, query the chaincode.

```
$ python query.py
```

See logs in the first console for details.
