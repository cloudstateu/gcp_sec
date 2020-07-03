1. Utworzenie bucketa w usłudze Cloud Storage
```
gsutil mb gs://<BUCKET_NAME>
```

2. Włączenie api dla usługi KMS
```
gcloud services enable cloudkms.googleapis.com
```

3. Utworzenie globalnego keyring
```
gcloud kms keyrings create <KEYRING_NAME>
```

4. Utworzenie symetrycznego klucza do szyfrowania danych
```
gcloud kms keys create <CRYPTOKEY_NAME> --location global --keyring <KEYRING_NAME> --purpose encryption
```

5. Sformatowanie tekstu z pliku do base64
```
TEXT=$(cat <FILE_NAME> | base64 -w0)
```

6. Wywołanie API do szyfrowania danych
```
curl -v "https://cloudkms.googleapis.com/v1/projects/$DEVSHELL_PROJECT_ID/locations/global/keyRings/$KEYRING_NAME/cryptoKeys/$CRYPTOKEY_NAME:encrypt" \
  -d "{\"plaintext\":\"$PLAINTEXT\"}" \
  -H "Authorization:Bearer $(gcloud auth application-default print-access-token)"\
  -H "Content-Type: application/json"
  | jq .ciphertext -r > <ENCRYPTED_FILE_NAME>
```

7. Wywołanie API do odszyfrowania danych
```
curl -v "https://cloudkms.googleapis.com/v1/projects/$DEVSHELL_PROJECT_ID/locations/global/keyRings/$KEYRING_NAME/cryptoKeys/$CRYPTOKEY_NAME:decrypt" \
  -d "{\"ciphertext\":\"$(cat <ENCRYPTED_FILE_NAME)\"}" \
  -H "Authorization:Bearer $(gcloud auth application-default print-access-token)"\
  -H "Content-Type: application/json"
```