# Codimd on Cloud Foundry


## Create PostgreSQL

```
cf create-user-provided-service codimd-db -p "{\"uri\": \"postgres://user:pass@hostname:port/dbname\"}"
```
or
```
cf create-service credhub default codimd-db -c "{\"uri\": \"postgres://user:pass@hostname:port/dbname\"}"
```

MySQL will also work.

## Create azure blob storage

```
export TENANT_ID=....
export CLIENT_ID=....
export CLIENT_SECRET=....
export LOCATION="Japan East"
export RESOURCE_GROUP=codimd
export STORAGE_ACCOUNT_NAME=makicodimdupload
export STORAGE_CONTAINER_NAME=upload-files

az login --username ${CLIENT_ID} \
         --password ${CLIENT_SECRET} \
         --service-principal \
         --tenant ${TENANT_ID} 

az group create --name ${RESOURCE_GROUP} \
  --location "${LOCATION}"

az storage account create --name ${STORAGE_ACCOUNT_NAME} \
  --resource-group ${RESOURCE_GROUP} \
  --location "${LOCATION}" \
  --sku "Standard_LRS"

az storage container create --name ${STORAGE_CONTAINER_NAME} \
  --account-name ${STORAGE_ACCOUNT_NAME}
    
CONNECTION_STRING=$(az storage account show-connection-string \
    --resource-group ${RESOURCE_GROUP} \
    --name ${STORAGE_ACCOUNT_NAME} \
    --query connectionString \
    --output tsv)
```



```
cf create-user-provided-service azure-blob -p "{\"conatiner\": \"${STORAGE_CONTAINER_NAME}\", \"connection_string\": \"${CONNECTION_STRING}\"}"
```
or
```
cf create-service credhub default -c "{\"conatiner\": \"${STORAGE_CONTAINER_NAME}\", \"connection_string\": \"${CONNECTION_STRING}\"}"
```


AWS S3 is also supported

## Create Bitbukcet OAuth

```
cf create-user-provided-service bitbucket-oauth -p "{\"client_id\": \"...\", \"client_secret\": \"...\"}"
```
or
```
cf create-service credhub default bitbucket-oauth -c "{\"client_id\": \"...\", \"client_secret\": \"...\"}" 
```

Bitbucket is just an exmple from a lot of choices.

## cf push

```
cf push
```

See [all configurations](https://hackmd.io/c/codimd-documentation/%2Fs%2Fcodimd-configuration).
