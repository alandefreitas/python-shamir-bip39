# python-shamir-bip39

This is a fork of [python-shamir-mnemonic](https://github.com/trezor/python-shamir-mnemonic), that uses BIP39 mnemonics for Shamir's algorithm instead of SLIP-0039 master secrets. [python-shamir-mnemonic](https://github.com/trezor/python-shamir-mnemonic) is the reference implementation of SLIP-0039: Shamir's Secret-Sharing for Mnemonic
Codes.

## Basic usage

### Install

```bash
git clone https://github.com/alandefreitas/python-shamir-bip39
cd python-shamir-bip39
python3 setup.py develop
```

### Usage

Creating 3-of-5 Shamir secrets for BIP39 mnemonics:

```bash
python3 -m shamir_mnemonic.cli create 3of5
```

Recover BIP39 mnemonics from 3-of-5 Shamir secrets:

```bash
python3 -m shamir_mnemonic.cli recover
```

Split BIP39 mnemonics that already exists into 3-of-5 Shamir secrets:

```bash
python3 -m shamir_mnemonic.cli create 3of5 --mnemonics='spike flat want risk laundry wasp round only bag sentence biology obey'
```

Split SLIP39 master key that already exists into 3-of-5 Shamir secrets:

```bash
python3 -m shamir_mnemonic.cli create 3of5 --master-secret=d1cb13dbdd37dbef2f1cd811987c59cb
```

Split BIP39 mnemonics with a custom scheme:

```bash
python3 -m shamir_mnemonic.cli create custom --group-threshold 3 --group 2 3 --group 2 5 --group 4 5
```

### Passphrase

This implementation uses the same source of entropy for the *mnemonics* and the *master secret*. This is not a problem since both contain the same information.

However, this means the `mnemonics` option cannot work safely with the `passphrase` option because BIP39 and SLIP39 specify different strategies to mix this source of entropy with the passphrase, which leads to different seeds.

This implementation will still show you both seeds, but we recommend using this implementation for the BIP39 mnemonics only and letting your wallet worry about the passphrase.   

## Motivation

It is very useful to use Shamir's secret to split BIP-0039 seeds directly:

- Software wallets would not have to implement one more protocol.
- Old hardware wallets would work with Shamir mnemonics.
- Custom BIP-0039 extension words would still work.
- The user wouldn't need to move funds from existing BIP-0039 wallets to SLIP-0039 wallets if they feel the original seed is not compromised.

## Abstract

This SLIP describes a standard and interoperable implementation of Shamir's
secret sharing (SSS). SSS splits a secret into unique parts which can be
distributed among participants, and requires a specified minimum number of
parts to be supplied in order to reconstruct the original secret. Knowledge of
fewer than the required number of parts does not leak information about the
secret.

## Specification

See https://github.com/satoshilabs/slips/blob/master/slip-0039.md for full
specification.

## Security

This implementation is not using any hardening techniques. Secrets are passed in the
open, and calculations are most likely trivially vulnerable to side-channel attacks.

The purpose of this code is to verify correctness of other implementations. **It should
not be used for handling sensitive secrets**.

CLI tool is included as a reference and UX testbed.

**Warning:** this tool makes no attempt to protect sensitive data! Use at your own risk.
If you need this to recover your wallet seeds, make sure to do it on an air-gapped
computer, preferably running a live system such as Tails.

## Test vectors

The test vectors in vectors.json are given as a list of triples. The first member of the
triple is a description of the test vector, the second member is a list of mnemonics and
the third member is the master secret which results from combining the mnemonics. The
master secret is encoded as a string containing two hexadecimal digits for each byte. If
the string is empty, then attempting to combine the given set of mnemonics should result
in error. The passphrase "TREZOR" is used for all valid sets of mnemonics.
