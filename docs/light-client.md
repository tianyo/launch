# Lambda Light client User Guide

## Basic Functionality Description

1. Provide restful APIs
2. Verify query proof

## Usage

Assuming we have set `lambdacli` config properly(`trust-node`, `node`, `chain-id` etc)

```bash
./lambdacli rest-server
```

## Flags

Name, shorthand | Default | Description | Required
:- | :- | :- | :-
--cors | | Set the domains that can make CORS requests | 
--indent | | Add indent to JSON response |
--laddr | tcp://localhost:1317 | The address for the server to listen on |
--ledger | | Use a connected Ledger device |
--max-open | 1000 | The number of maximum open connections |
--node | tcp://localhost:26657 | [host]:[port] to tendermint rpc interface for this chain |
--trust-node | | Starting IP address | Trust connected full node (don't verify proofs for responses)
--chain-id | | Chain ID of tendermint node

## REST APIs

Here we just list all APIs and introduce their functionality briefly.

1. Tendermint APIs, such as query blocks, transactions and validator set

    - `GET /node-info`: The properties of the connected node
    - `GET /syncing`: Syncing state of node
    - `GET /blocks/latest`: Get the latest block
    - `GET /blocks/{height}`: Get a block at a certain height
    - `Get /version` Get the version of lambdacli
    - `Get /node_version` Get Version of the connected node
    - `GET /validatorsets/latest`: Get the latest validator set
    - `GET /validatorsets/{height}`: Get a validator set at a certain height
    - `GET /txs/{hash}`: Get a Tx by hash
    - `GET /txs`: Search transactions

2. Broadcast transactions

    - `POST /tx/broadcast`: Broadcast a signed StdTx which is amino or json encoded
    - `POST  /txs/encode`: Encode a transaction to the Amino wire format

3. Bank module APIs

    - `GET /bank/balances/{address}`: Get the account balances
    - `POST /bank/accounts/{address}/transfers`: Send coins from one account to another
    - `GET /auth/accounts/{address}`: Get the account information on blockchain
    
4. Staking module APIs

    - `GET /staking/delegators/{delegatorAddr}/delegations`: Get all delegations from a delegator
    - `POST /staking/delegators/{delegatorAddr}/delegations`: Submit delegation
    - `GET /staking/delegators/{delegatorAddr}/delegations/{validatorAddr}`: Query the current delegation between a delegator and a validator
    - `GET /staking/delegators/{delegatorAddr}/unbonding_delegations`: Get all unbonding delegations from a delegator
    - `POST /staking/delegators/{delegatorAddr}/unbonding_delegations`: Submit an unbonding delegation
    - `GET /staking/delegators/{delegatorAddr}/unbonding_delegations/{validatorAddr}`: Query all unbonding delegations between a delegator and a validator
    - `GET /staking/redelegations`: Get all redelegations (filter by query params)
    - `POST /staking/delegators/{delegatorAddr}/redelegations`: Submit a redelegation
    - `GET /staking/delegators/{delegatorAddr}/validators`: Query all validators that a delegator is bonded to
    - `GET /staking/delegators/{delegatorAddr}/validators/{validatorAddr}`: Query a validator that a delegator is bonded to
    - `GET /staking/delegators/{delegatorAddr}/txs`: Get all staking txs (i.e msgs) from a delegator
    - `GET /staking/validators`: Get all validator candidates. By default it returns only the bonded validators
    - `GET /staking/validators/{validatorAddr}`: Query the information from a single validator
    - `GET /staking/validators/{validatorAddr}/delegations`: Get all delegations from a validator
    - `GET /staking/validators/{validatorAddr}/unbonding_delegations`: Get all unbonding delegations from a validator
    - `GET /staking/pool`: Get the current state of the staking pool
    - `GET /staking/parameters`: Get the current staking parameter values
    - `GET /staking/delegators/{delegatorAddr}/partner_delegations`: Get all partner delegations from a delegator
    - `GET /staking/partner_validators`: Get all partner validators
    - `GET /staking/partner_validators/{validatorAddr}`: Get a single partner validator info
    - `GET /staking/partner_validators/{validatorAddr}/delegations`: Get all delegations to a partner validator
    - `GET /staking/partner_validators/{validatorAddr}/unbonding_delegations`: Get all unbonding delegations from a partner validator
    - `GET /staking/delegators/{delegatorAddr}/partner_validators`: Query all partner validators that a delegator is bonded to
    - `GET /staking/delegators/{delegatorAddr}/partner_validators/{validatorAddr}`: Query a partner validator that a delegator is bonded to
    - `GET /staking/delegators/{delegatorAddr}/partner_delegations/{validatorAddr}`: Query a delegation between a delegator and a partner validator
    - `GET /staking/delegators/{delegatorAddr}/unbonding_partner_delegations/{validatorAddr}`: Query all unbonding delegations between a delegator and a partner validator
    - `GET /staking/supply_pool`: Get the current state of the supply pool

5. Slashing module APIs

    - `GET /slashing/validators/{validatorPubKey}/signing-info`: Get sign info of given validator
    - `GET /slashing/signing_infos`: Get sign info of given all validators
    - `POST /slashing/validators/{validatorAddr}/unjail`: Unjail a jailed validator
    - `GET /slashing/parameters`: Get the current slashing parameters

6. Governance module APIs

    - `POST /gov/proposals`: Submit a proposal
    - `GET /gov/proposals`: Query proposals
    - `POST /gov/proposals/{proposalId}/deposits`: Deposit tokens to a proposal
    - `GET /gov/proposals/{proposalId}/deposits`: Query deposits
    - `POST /gov/proposals/{proposalId}/votes`: Vote a proposal
    - `GET /gov/proposals/{proposalId}/votes`: Query voters
    - `GET /gov/proposals/{proposalId}`: Query a proposal
    - `GET /gov/proposals/{proposalId}/deposits/{depositor}`: Query deposit
    - `GET /gov/proposals/{proposalId}/votes/{voter}`: Query vote
    - `GET /gov/proposals/{proposalId}/proposer`: Query proposer
    - `GET /gov/proposals/{proposalId}/tally`: Get a proposal's tally result at the current time
    - `GET /gov/parameters/deposit`: Query governance deposit parameters
    - `GET /gov/parameters/tallying`: Query governance tally parameters
    - `GET /gov/parameters/voting`: Query governance voting parameters

7. Distribution module APIs

    - `GET /distribution/delegators/{delegatorAddr}/rewards`: Get the total rewards balance from all delegations
    - `POST /distribution/delegators/{delegatorAddr}/rewards`: Withdraw all the delegator's delegation rewards
    - `GET /distribution/delegators/{delegatorAddr}/rewards/{validatorAddr}`: Query a delegation reward
    - `POST /distribution/delegators/{delegatorAddr}/rewards/{validatorAddr}`: Withdraw a delegation reward
    - `GET /distribution/delegators/{delegatorAddr}/withdraw_address`: Get the rewards withdrawal address
    - `POST /distribution/delegators/{delegatorAddr}/withdraw_address`: Replace the rewards withdrawal address
    - `GET /distribution/validators/{validatorAddr}`: Fee distribution outstanding rewards of a single validator
    - `GET /distribution/validators/{validatorAddr}/rewards`: Commission and self-delegation rewards of a single validator
    - `POST /distribution/validators/{validatorAddr}/rewards`: Withdraw the validator's rewards
    - `GET /distribution/community_pool`: Community pool parameters
    - `GET  /distribution/parameters`: Fee distribution parameters

8. Mint module APIs

    - `GET /minting/parameters`: Minting module parameters
    - `GET /minting/inflation`: Current minting inflation value
    - `GET /minting/annual-provisions`: Current minting annual provisions value
   
7. Asset module APIs

    - `GET /asset/pledge/{address}`: Get the account's pledges
    - `GET /asset/pool"`: Get asset pool
    - `GET /asset/parameters`:  Get asset module parameters
    - `GET /asset/pairs/{asset}`: Get exchange pairs by the given asset
    - `POST /asset/pledge`: Pledge token to get asset
    - `POST /asset/drop`: Drop asset to get token