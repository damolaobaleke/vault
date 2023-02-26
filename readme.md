## Running a vault prod server
1. Create a directory using `mkdir -p vault/data` The vault config is stored here
2. create vault-config.hcl file and set its properties
    ```  
        ui = true
        disable_mlock = true #prevent memory from being swapped to disk

        storage "raft" {
        path    = "./vault/data"
        node_id = "node1"
        }

        listener "tcp" {
        address     = "0.0.0.0:8200"
        tls_disable = "true"
        }

        api_addr = "http://127.0.0.1:8200"
        cluster_addr = "https://127.0.0.1:8201"
    ```
3. run `vault server -config=./vault-config.hcl`

### Initialize vault server
1. run the cmd <b>`$ export VAULT_ADDR=http://localhost:8200`</b> this is what vault is listening on
2. check you're able to connect to the vault server using `vault status`
3. you might see Initialized value as <b>False</b>, this means vault server is running but hasn't been initialized
4. To initialize vault run cmd `vault operator init -key-shares=1 -key-threshold=1`
5. store vault token and unseal key
6. run the cmd `export VAULT_TOKEN=token` to communicate with vault
7. unseal vault `vault operator unseal`

### Enable vault server logs
1. create logs directory
2. set log file dir `vault audit enable file file_path=./logs/audit.log`
3. you can use jq to parse json in terminal and see logs as json in terminal `tail -f ./logs/audit.log | jq`
4. if you dont have jq install it using brew on mac, and for other OS use your package manager

### Access the ui
1. go to http://localhost:8200
2. authenticate using your root token

### Storing secrets in vault.
1. To store K,V secrets in vault enable the kv engine using `vault secrets enable -path="kv-v1" -description="K/V v1" kv`
2. Check the secrets engines enabled using `vault secrets list` you should now see it under the list of enabled engines

| Path            | Type            | Accessor        |  Description |
| --------------- | --------------- | --------------- |  ------------|
| cubbyhole/      | cubbyhole       | cubbyhole_167e309c | per-token private secret storage
| identity/       | identity        | identity_8e1a9832  | identity store
| <b>kv-v1/</b>      |   kv            | kv_dc8d4e16        |   Test K/V v1
| sys/            | system          | system_5b73c140    | system endpoints used for control, policy and debugging


3. Now to store a key-valur pair use cmd `vault kv put kv-v1/<DEFINE PATH> <KEY>=VALUE>`<br/> e.g 
<b>`vault kv put kv-v1/aws-apiKey awsApiKey=AAaaBBccDDeeOTXzSMT1234BB_Z8JzG7JkSVxI`</b>

4.Check your UI

#### Happy storing secrets! :rocket:
 
