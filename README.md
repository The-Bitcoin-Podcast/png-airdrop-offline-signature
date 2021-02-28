# png-airdrop-offline-signature

## Introduction

[Pangolin](https://pangolin.exchange/) is a new DEX which debuted on the [Avalanche](https://www.avalabs.org/) mainnet on [February 9, 2021](https://twitter.com/pangolindex/status/1359245703592218627). It has offered an airdrop of its PNG token to anyone holding UNI or SUSHI in an Ethereum wallet as of Dec 7, 2020. The [claim process](https://pangolin.exchange/tutorials/claim-png) relies primarily on using MetaMask, which unfortunately doesn't work for Trezor wallets on the Avalanche side. The deadline to claim this airdrop is "one month of Pangolin’s launch" (unclear if this means 30 or 31 days). Its unlikely Trezor will be patched to worked with MetaMask before this deadline. The following tutorial shows how to load a Trezor seed phrase into an offline temporary computer and through a reasonably safe method transfer a signed transaction back to an online computer and publish it to the Avalanche network to claim an airdrop.

## Requirements

* Computer that can be booted by USB flash drive
* USB flash drive loaded with 64-bit [Xubuntu 20.04](https://xubuntu.org/release/20-04/)
* Cell phone with [Google Lens app](https://play.google.com/store/apps/details?id=com.google.ar.lens&hl=en_US&gl=US)

## Prepare Python and segno QR Code Programs

Boot computer with USB Flash drive loaded with Xubuntu 20.04. Machine should be connected to the Internet for the beginning steps:

```bash
cd  # start in home directory
sudo apt update
sudo apt install -y build-essential git python3-dev python3-venv
python3 -m venv venv
source venv/bin/activate
git clone https://github.com/heuer/segno.git  # segno==1.3.2.dev0 at time of writing
cd segno  # return to home directory
python3 setup.py install
cd
pip3 install web3  # 5.16.0 at time of writing
python3
```
You should now be in a Python 3.8 interactive session.

## Load Web3 and Pangolin Claim Contract

The following will be a pain to type in, but its worth it to do manually for good security.

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
```
