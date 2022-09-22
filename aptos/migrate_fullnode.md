<strong><p style="font-size:14px" align="left">Founder :
<a href="https://discord.gg/JqQNcwff2e" target="_blank">NodeX Capital Discord</a></p></strong>
<strong><p style="font-size:14px" align="left">Visit Our Website : 
<a href="https://nodex.codes/" target="_blank">https://nodex.codes</a></p></strong>
<strong><p style="font-size:14px" align="left">Follow Me :
<a href="https://twitter.com/nodexploit/" target="_blank">NodeX Twitter</a></p></strong>
<strong><p style="font-size:14px" align="left">Motto :
<a>I want to be a useful person in the community✨</a></p></strong>
<hr>

<p align="center">
  <img width="100" height="auto" src="https://user-images.githubusercontent.com/50621007/165930080-4f541b46-1ae3-461c-acc9-de72d7ab93b7.png">
</p>

# Migrate your fullnode to another machine
## To find your keys on existing server run command below:
```
echo -e "Your private key: \e[1m\e[32m$KEY\e[0m \nYour peer_id: \e[1m\e[32m$PEER_ID\e[0m"
```

## To migrate your fullnode to another machine you need to follow these simple steps
1. Connect to new fresh server
2. Fill in your identity keys and execute commands bellow
```
echo "export KEY=<YOUR_KEY>" >> $HOME/.bash_profile
echo "export PEER_ID=<YOUR_PEER_ID>" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
3. Start your installation by following automatic or manual aptos fullnode installation that you can find [here](https://github.com/kj89/testnet_manuals/blob/main/aptos/README.md)
