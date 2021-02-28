# png-airdrop-offline-signature

## Introduction

[Pangolin](https://pangolin.exchange/) is a new DEX which debuted on the [Avalanche](https://www.avalabs.org/) mainnet on [February 9, 2021](https://twitter.com/pangolindex/status/1359245703592218627). It has offered an airdrop of its PNG token to anyone holding UNI or SUSHI in an Ethereum wallet as of Dec 7, 2020. The [claim process](https://pangolin.exchange/tutorials/claim-png) relies primarily on using MetaMask, which unfortunately doesn't work for Trezor wallets on the Avalanche side. The deadline to claim this airdrop is "one month of Pangolin’s launch" (unclear if this means 30 or 31 days). Its unlikely Trezor will be patched to worked with MetaMask before this deadline. The following tutorial shows how to load a Trezor seed phrase into an offline temporary computer and through a reasonably safe method transfer a signed transaction back to an online computer and publish it to the Avalanche network to claim an airdrop.
