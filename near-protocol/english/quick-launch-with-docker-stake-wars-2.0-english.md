# Quick launch with Docker Stake Wars 2.0 \(English\)

[![ERM](https://miro.medium.com/fit/c/96/96/1*ncl_cgTAdxoj4eZTrTm1gA.jpeg)](https://medium.com/@narniec2020?source=post_page-----ddaf9940c3c1----------------------)

If you want to get your feet wet, and see if [Stake Wars](https://near.org/stakewars/) is for you, try this quickstart guide:

* Create a **BetaNet** account using our hosted wallet [here](https://wallet.betanet.near.org/).
* Spin up an Ubuntu/Debian VPS and run [nearup](https://github.com/near/nearup).
* Deploy your staking pool using the [Staking Pool Factory](https://near-examples.github.io/staking-pool-factory/)
* Submit up [this form](https://nearprotocol1001.typeform.com/to/TvvOMf) to enroll in the Stake Wars.

This simple guide will help you install Near-Shell, Nearup, also Deploy your staking pool using the Staking Pool Factory and edit the **validator\_key.json**

[**Hardware Requirements**](https://docs.near.org/docs/roles/validator/hardware)**:**

* CPU: 2-Core \(4-Thread\) Intel i7/Xeon equivalent
* RAM: 4GB DDR4
* Storage: 100GB

**\#Update system**

```text
sudo apt update && sudo apt upgrade -y
sudo apt install python3 git curl -y
```

**\#Near-Shell**

```text
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ~/.profile && source ~/.bashrc
nvm install v12.0.0
npm i -g near-shell --unsafe-perm
echo -n "export NODE_ENV=betanet" >> ~/.profile
source ~/.profile
near login
```

**\#Nearup**

```text
curl --proto '=https' --tlsv1.2 -sSfL https://up.near.dev | python3
source $HOME/.nearup/env
sudo apt install docker.io
sudo usermod -aG docker ${USER}
exit
nearup betanet
docker logs --f nearcore
```

**\#Edit validator\_key**

```text
nearup stop
cat ~/.near/betanet/validator_key.json | grep 'public_key\|account_id'
sudo nano .near/betanet/validator_key.json
rm -rf ~/.near/betanet/data
nearup betanet
```

