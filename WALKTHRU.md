# Step 1: Create a key pair
``` bash
didkit generate-ed25519-key > issuer_key.jwk
```

# Step 2: Create a DID from the key pair
``` bash
didkit key-to-did key -k issuer_key.jwk
```

# Step 3: Create a verification method
``` bash
didkit key-to-verification-method -k issuer_key.jwk
```

# Step 3: Issue a credential 

``` bash
curl -fsS $didkit_url/issue/credentials \
    -H 'Content-Type: application/json' \
     -o credential-signed.jsonld \
     -d '{
   "credential": {
        "@context": "https://www.w3.org/2018/credentials/v1",
        "id": "http://example.org/credentials/3731",
        "type": ["VerifiableCredential"],
        "issuer": "did:key:z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg",
        "issuanceDate": "2020-08-19T21:41:50Z",
        "credentialSubject": {
            "id": "did:example:d23dd687a7dc6787646f2eb98d0"
        }
    },
   "options": {
        "verificationMethod": "$verification_method",
        "proofPurpose": "assertionMethod"
   }
 }'
```

Take a look at the signed credential:
``` json
{
  "@context": "https://www.w3.org/2018/credentials/v1",
  "id": "http://example.org/credentials/3731",
  "type": ["VerifiableCredential"],
  "credentialSubject": {
    "id": "did:example:d23dd687a7dc6787646f2eb98d0"
  },
  "issuer": "did:key:z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg",
  "issuanceDate": "2020-08-19T21:41:50Z",
  "proof": {
    "type": "Ed25519Signature2018",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:key:z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg#z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg",
    "created": "2021-07-19T14:43:44.331Z",
    "jws": "eyJhbGciOiJFZERTQSIsImNyaXQiOlsiYjY0Il0sImI2NCI6ZmFsc2V9..6oZf-_M3nE_i9Ut90m0dG5xSusolwY0JKfKNEVxcYkz8JcY-HAegHY2G8kjrJo5-ZyKnt_hn7HDdTaxmU473Ag"
  }
}
```

# Step 4: Verify Credential

``` bash
curl -fsS $didkit_url/verify/credentials -H 'Content-Type: application/json' -d '{
        "verifiableCredential": {
            "@context": "https://www.w3.org/2018/credentials/v1",
            "id": "http://example.org/credentials/3731",
            "type": ["VerifiableCredential"],
            "credentialSubject": {
                "id": "did:example:d23dd687a7dc6787646f2eb98d0"
            },
            "issuer": "did:key:z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg",
            "issuanceDate": "2020-08-19T21:41:50Z",
            "proof": {
                "type": "Ed25519Signature2018",
                "proofPurpose": "assertionMethod",
                "verificationMethod": "did:key:z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg#z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg",
                "created": "2021-07-19T14:43:44.331Z",
                "jws": "eyJhbGciOiJFZERTQSIsImNyaXQiOlsiYjY0Il0sImI2NCI6ZmFsc2V9..6oZf-_M3nE_i9Ut90m0dG5xSusolwY0JKfKNEVxcYkz8JcY-HAegHY2G8kjrJo5-ZyKnt_hn7HDdTaxmU473Ag"
            }
        },
        "options": {
            "verificationMethod": "did:key:z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg#z6MkedgaoGdmcDnRw6nkksV4J2vza2wnB7Vv6NASA6M1Esbg",
            "proofPurpose": "assertionMethod"
        }
    }'
```

