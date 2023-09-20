# Setup Mainnet Polygon zkEVM node

This is a guide on how to setup a Polygon zkEVM node

#### Requirements:

1. 500GB HDD Space
2. Go version 19.4
3. Make sure to have PostgreSQL pg_restore v15.4 installed. 
You can install it by following the below script:
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget -qO- https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo tee /etc/apt/trusted.gpg.d/pgdg.asc &>/dev/null
sudo apt update
sudo apt install postgresql-client-15
```

#### Step 1: Download the following snapshots

1. https://zkevm-mai.s3.eu-west-1.amazonaws.com/zkevmmainnetdbhash_1695053524_v0.3.0-1-g375bffaf_375bffaf.sql.tar.gz 
2. https://zkevm-mai.s3.eu-west-1.amazonaws.com/zkevmmainnetdbstate_1695050997_v0.3.0-1-g375bffaf_375bffaf.sql.tar.gz

#### Step 2: Edit the .env file with your Ethereum L1 endpoint

Edit the .env file with your Ethereum L1 endpoint here
```bash
ZKEVM_NODE_ETHERMAN_URL = "http://your.L1node.url"
```

#### Step 3: Start the Docker container for zkevm-state-db

```
docker-compose up -d zkevm-state-db
```

#### Step 4: Clear the statedb

```bash
cd data
rm -rf statedb && mkdir statedb
```

## Step 5: Install the zkevm-node binary by following the below script
```bash
git clone https://github.com/0xPolygonHermez/zkevm-node.git`
cd zkevm-node
git checkout release/v0.3.0
make build
mv dist/zkevm-node ~/zkevm-node-bin
```

#### Step 6: Run the following commands to restore the db

```
cd config/environments/mainnet
nohup ~/zkevm-node-bin restore --cfg ./node.snapshot.config.toml -is ~/zkevmmainnetdbstate_1695050997_v0.3.0-1-g375bffaf_375bffaf.sql.tar.gz -ih ~/zkevmmainnetdbhash_1695053524_v0.3.0-1-g375bffaf_375bffaf.sql.tar.gz &
```

#### Step 7: Spin up the other containers

```
# Run this in the root directory of this repository
docker-compose up -d
```

## Resources

1. https://polygontechnology.notion.site/zkEVM-mainnet-beta-Instructions-for-permissionless-nodes-to-rehash-the-network-53b525498bc94c03ab02bba621f8d8e3