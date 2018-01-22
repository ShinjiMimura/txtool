Transaction creation and offline signing tools
==============================================

Overview
--------

The script `txtool-build` lets you build a transaction, while bundling it with
all the info an offline PC needs to verify that it is correct and to sign it
(except the private keys).

Then `txtool-sign` can sign this transaction using only the data provided by
`txtool-build` and a text file containing one private key per line.

These scripts use `bitcoin-cli` (or variants) to fetch information about
transactions the created transaction depends on and get an initial (empty)
transaction. Then `bitcoin-tx` (or variants) is used to populate and sign the
transaction.

Since most bitcoin forks include these programs, these scripts can be used to
create transactions to split your coins without relying on centralized
services.

Testing
-------

The script 'txtool-test' runs a bitcoin daemon in a temporary directory,
performs the complete workflow and finally checks if the generated transaction
is accepted by the daemon. If all goes well (and the debug flag '-D' is not
specified), no output is generated.

Installation
------------

Move `txtool-buid`, `txtool-sign` and `txtool.functions` to somewhere in the
`PATH`, for example `/usr/local/bin/`.

Simple guide to splitting BCD
-----------------------------

- decide from which addresses you want to move your BCD

- move BTC from these addresses

- take an online computer and compile and install Bitcoin Diamond full node
from `https://github.com/eveybcd/BitcoinDiamond` (if you already have a
`$HOME/.bitcoin` directory, you need to set a -datadir for bitcoindiamond so
that it does not thrash that directory)

- use `bitcoindiamond-cli importaddress` to import the addresses from which you
want to move your BCD (this only accept addresses that start with a one,
however if you enable the p2sh option, the corresponding P2SH-P2WKH will also
be added).

- let the blockchain sync

- create `~/.txtool.BCD` in which you point to `bitcoindiamond-tx` and
`bitcoindiamond-cli` and set `CORE_ARGS=` to `-datadir=...` if you do not use
the default datadir.

- get an address to which you want to move your BCD and do
  `$ bitcoindiamond-cli listunspent | txtool-build --conf BCD ADDRESS 0.001 > tosign.json`

- move the txtool repository, `.txtool.BCD`, `tosign.json` and
`bitcoindiamond-tx` to and offline computer and create on this computer a
textfile with all the needed private keys (one per line)

- do `$ txtool-sign privkeys.txt < tosign.json > signed.tx` on the offline
computer, read the output of this program to see if the transaction does what
you want.

- move `signed.tx` to the online computer and broadcast the transaction with
`bitcoindiamond-cli sendrawtransaction $(cat signed.tx)`

- hope for the best

Donations
---------

If you find these scripts useful, please consider donating any amount
of of any bitcoin fork to 1157ieoVosj5Qb7DGQHe1qUJXjmKCur8H3.

