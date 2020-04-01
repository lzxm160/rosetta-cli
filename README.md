# rosetta-validator

[![Coinbase](https://circleci.com/gh/coinbase/rosetta-validator/tree/master.svg?style=svg)](https://circleci.com/gh/coinbase/rosetta-validator/tree/master)

Once you create a Rosetta Server, you'll need to test its
performance and correctness. This validation tool makes that easy!

## What is Rosetta?
Rosetta is a new project from Coinbase to standardize the process
of deploying and interacting with blockchains. With an explicit
specification to adhere to, all parties involved in blockchain
development can spend less time figuring out how to integrate
with each other and more time working on the novel advances that
will push the blockchain ecosystem forward. In practice, this means
that any blockchain project that implements the requirements outlined
in this specification will enable exchanges, block explorers,
and wallets to integrate with much less communication overhead
and network-specific work.

## Run the Validator
1. Start your Rosetta Server (and the blockchain node it connects to if it is
not a single binary.
2. Modify the `Makefile` to point to the correct Rosetta Server port.
3. Start the validator using `make validator`.
4. Examine processed blocks using `make watch-blocks`. You can also print transactions
by setting `LOG_TRANSACTIONS="true"` in the `Makefile`.
5. Watch for errors in the processing logs. Any error will cause the validator to stop.
6. Analyze benchmarks from `worker-data/block_benchmarks.csv` and
  `worker-data/account_benchmarks.csv` by setting `LOG_BENCHMARKS="true"` in the `Makefile`.

_There is no additional setting required to support blockchains with reorgs. This
is handled automatically!_

## Development
* `make deps` to install dependencies
* `make test` to run tests
* `make lint` to lint the source code (included generated code)

## Correctness Checks
This tool performs a variety of correctness checks using the Rosetta Server. If
any correctness check fails, the validator will exit and print out a detailed
message explaining the error.

### Response Correctness
The validator uses the autogenerated [Go Client package](https://github.com/coinbase/rosetta-sdk-go)
to communicate with the Rosetta Server and assert that responses adhere
to the Rosetta Standard.

### Duplicate Hashes
The validator checks that a block hash or transaction hash is
never duplicated.

### Non-negative Balances
The validator checks that an account balance does not go
negative from any operations.

### Balance Reconciliation
#### Active Addresses
The validator checks that the balance of an account computed by
its operations is equal to the balance of the account according
to the node. If this balance is not identical, the validator will
exit.

#### Inactive Addresses
The validator randomly checks the balances of accounts that aren't
involved in any transactions. The balances of accounts could change
on the blockchain node without being included in an operation
returned by the Rosetta Server. Recall that ALL balance-changing
operations must be returned by the Rosetta Server.

## Future Work
* Automatically test the correctness of a Rosetta Client SDK by constructing,
signing, and submitting a transaction. This can be further extended by ensuring
broadcast transactions eventually land in a block.
* Change logging to utilize a more advanced output mechanism than CSV.

## License
This project is available open source under the terms of the [Apache 2.0 License](https://opensource.org/licenses/Apache-2.0).

© 2020 Coinbase
