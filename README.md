# png-airdrop-offline-signature

## Introduction

[Pangolin](https://pangolin.exchange/) is a new DEX which debuted on the [Avalanche](https://www.avalabs.org/) mainnet on [February 9, 2021](https://twitter.com/pangolindex/status/1359245703592218627). It has offered an airdrop of its PNG token to anyone holding UNI or SUSHI in an Ethereum wallet as of Dec 7, 2020. The [claim process](https://pangolin.exchange/tutorials/claim-png) relies primarily on using MetaMask, which unfortunately doesn't work for Trezor wallets on the Avalanche side. The deadline to claim this airdrop is "one month of Pangolin’s launch" (unclear if this means 30 or 31 days). Its unlikely Trezor will be patched to worked with MetaMask before this deadline. The following tutorial shows how to load a Trezor seed phrase into an offline temporary computer and through a reasonably safe method transfer a signed transaction back to an online computer and publish it to the Avalanche network to claim an airdrop. Given the rapidly approaching deadline for the airdrop, the initial focus here is to claim the PNG. Further steps will be published in the next few days to show how to move C-AVAX and ERC20 tokens out of a Trezor wallet.

## Requirements

* Computer that can be booted by USB flash drive
* USB flash drive loaded with 64-bit [Xubuntu 20.04](https://xubuntu.org/release/20-04/)
* Cell phone with [Google Lens app](https://play.google.com/store/apps/details?id=com.google.ar.lens&hl=en_US&gl=US)
* Before starting the tutorial, send at least 0.5 C-AVAX to your Trezor C-chain address (same address as your Trezor Ethereum address) to use for gas costs. Note: upcoming edits to this tutorial will show you how to withdraw remaining C-AVAX funds back to a non-Trezor wallet.
* Before starting the tutorial, use a non-Trezor account on Pangolin to buy 1 UNI and/or 1 SUSHI (depending on which tokens you held on Dec 7, 2020 on Ethereum) and transfer it to your Trezor C-AVAX address. Note: upcoming edits will show you how to withdraw ERC20 tokens to your non-Trezor wallet.

## Prepare Python and segno QR Code Programs (ONLINE)

Boot computer with USB Flash drive loaded with Xubuntu 20.04. Machine should be connected to the Internet for the beginning steps:

```bash
cd  # start in home directory
sudo apt update
sudo apt install -y build-essential git python3-dev python3-venv
python3 -m venv venv
source venv/bin/activate
git clone https://github.com/heuer/segno.git  # segno==1.3.2.dev0 at time of writing
cd segno
python3 setup.py install
cd  # return to home directory
pip3 install web3  # 5.16.0 at time of writing
python3
```

## Load Web3 and Pangolin Claim Contract (ONLINE)

You should now be in a Python 3.8 interactive session. The following will be a pain to type in, but its worth it to do manually for good security. The following must be done while connected to the Internet.

```python3
from web3 import Web3, HTTPProvider
claim_abi = """\
[
  {
	"inputs": [],
	"name": "claim",
	"outputs": [],
	"stateMutability": "nonpayable",
	"type": "function"
  }
]
"""
provider = HTTPProvider("https://api.avax.network/ext/bc/C/rpc")
w3 = Web3(provider)
pangolin = w3.eth.contract(address="0x0C58C2041da4CfCcF5818Bbe3b66DBC23B3902d9", abi=claim_abi)
tx = {}
tx["gas"] = 100000
tx["gasPrice"] = Web3.toWei(470, "Gwei")  # the default C-chain gas price at time of writing
tx["nonce"] = 0   # assumes this is your first tx ever for trezor avax side
tx["chainId"] = 43114  # avax mainnet
claim_tx = pangolin.functions.claim().buildTransaction(tx)
```

## Load Your Trezor Account (OFFLINE)

Keep the python interactive session open. However, the following must be done with your machine offline. Are you offline now? Good. Proceed with no one looking over your shoulder, or web cams watching:

```python3
from web3.eth import Account
Account.enable_unaudited_hdwallet_features()
```

If your Trezor has a seed phrase + passphrase, do this to load your account:

```python3
from_acct = Account.from_mnemonic("lowercase space separated seed phrase", "your passphrase")
```

If your Trezor has a seed phrase + passphrase, and lives on a different derivation path, do this to load your account:

```python3
from_acct = Account.from_mnemonic("lowercase space separated seed phrase", "your passphrase", "m/44'/60'/0'/0/[update derivation path here]")
```

Otherwise, if your Trezor uses only a seed phrase:

```python3
from_acct = Account.from_mnemonic("lowercase space separated seed phrase")
```

If Python complains you don't have a valid seed phrase, make sure you made no typos in the words. See the [official English dictionary for seed phrases (BIP39)](https://github.com/bitcoin/bips/blob/master/bip-0039/english.txt).

Confirm your address is what you expect it to be. If its wrong, do not proceed.

```python3
print(from_acct.address)  # make sure it matches what you expect
```

## Sign the Claim Transaction (OFFLINE)

Hit `Enter` a zillion times to clear the screen of your seed phrase line and/or passphrase information. Next, the following will generate a QR code version of your signed transaction:

```python3
cmd = f"segno {from_acct.sign_transaction(claim_tx).rawTransaction.hex()}"
import os
os.system(cmd)
```

The QR code should appear directly in the terminal. Use the Xubuntu terminal `View: Zoom Out` menu option as many times as needed. Use Google Lens to get its value onto your cell phone's clipboard. Then email it to yourself. Since its a signed transaction, its safe to do. Keep in mind this portion of data will be published to a public blockchain. At this point, you can turn off your temporary Xubuntu machine. Its no longer needed.

## Time to Claim Your Destiny (ONLINE)

At this point, you can either reboot your computer using the Xubuntu flash drive, or use another normal   computer with web3 and python3 installed. In Python:

```python3
from web3 import Web3, HTTPProvider
provider = HTTPProvider("https://api.avax.network/ext/bc/C/rpc")
w3 = Web3(provider)
signed_rawTransaction = "my tranaction hex code"
w3.eth.sendRawTransaction(signed_rawTransaction)
```

The resulting `HexBytes('Ox...')` indicates the transaction has been submitted to the Avalanche C-chain. You can look for it at [Avascan](https://avascan.info/).

## Next Steps (COMING SOON TO THIS TUTORIAL)

* Show how to move PNG and other ERC20 tokens from a Trezor wallet to a non-Trezor wallet.
* How to withdraw any existing AVAX stored in your Trezor wallet.
