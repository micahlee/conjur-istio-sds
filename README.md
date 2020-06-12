# Conjur / istio Demo

This is an exploration of using Conjur as the certificate authority for istio
mutual TLS.

## What Works

- Running istio in Kubernetes in Docker (kind)
- Deploying the sample book store application from istio
- Retrieving the istio-proxy certificate from pod

    Example:

    ```sh-session
    $ kubectl exec reviews-v3-58fc46b64-gj28x -c istio-proxy -- openssl s_client -showcerts -connect productpage.default:9080
    depth=1 O = cluster.local
    verify error:num=19:self signed certificate in certificate chain
    140405425123776:error:1409445C:SSL routines:ssl3_read_bytes:tlsv13 alert certificate required:../ssl/record/rec_layer_s3.c:1528:SSL alert number 116
    CONNECTED(00000005)
    ---
    Certificate chain
    0 s:
      i:O = cluster.local
    -----BEGIN CERTIFICATE-----
    MIIDLDCCAhSgAwIBAgIQZWdKr0raAFnl3nJUvrKkyDANBgkqhkiG9w0BAQsFADAY
    MRYwFAYDVQQKEw1jbHVzdGVyLmxvY2FsMB4XDTIwMDYxMjE3NTUxNloXDTIwMDYx
    MzE3NTUxNlowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAO1Wg8sJ
    eedPzV3k0Zg2KjS2jquGc6ojdthFNNFcu0OEq6sg/R4RLK0SpRsMq5oxts3sI1JM
    8/NvUjq9ARk8jMwcnvjYtqYCzQ9N4wsv1YLPtc/oJIjd+o6/I9UxFuDOSaFGinsJ
    Etj49r1Wwp9nHegniqM3kSv4R1LllK7QFk+6tZXRKz7yiScWK9QdzFMjjZe3Qyb1
    Wtap9rBqes54byEGYZavAZ0AlrKspbhTo0I4sCDj1EfchPYCMKpEQsiAm6jGx/CZ
    p70jExQa3qpYgTr20i8DwaWG5KVkaECloSFZmB7KzcLYnJQpgQLYyxGVlmnWGICU
    XVD/CNYTCFcWdoUCAwEAAaOBiTCBhjAOBgNVHQ8BAf8EBAMCBaAwHQYDVR0lBBYw
    FAYIKwYBBQUHAwEGCCsGAQUFBwMCMAwGA1UdEwEB/wQCMAAwRwYDVR0RAQH/BD0w
    O4Y5c3BpZmZlOi8vY2x1c3Rlci5sb2NhbC9ucy9kZWZhdWx0L3NhL2Jvb2tpbmZv
    LXByb2R1Y3RwYWdlMA0GCSqGSIb3DQEBCwUAA4IBAQAK0DSsA4+KPu1aHUbgJvgY
    CJOG3zBxrEQ5eFk5zBYjkErEf8L3ZyXnVPchkt+JvD2DDmoI/MmaITkbQFlcttra
    fXvixBmoD1TM5bqlhFrhLiuLZMQdncgVsuKoZO1CLUGNuQIRxcvqjr9v2cX7eHWN
    pq87uPYbBnCwHXwV4pfMjy6Jj8JMcpdwlcQqvzORfv524bgLnZ3/Z95ezpJN3pe5
    hOKmZbd97b/TA/ZeF1tcVJuZpkRX2jyCI28KKKaz02hhKkPZqnn5jj7tOb+ArT9T
    riY8S3U+J16fO64Tkzl8LsIgTXm2YQCbCycfx33Uox+B7wExBQHfc8nHCkxqbpLm
    -----END CERTIFICATE-----
    1 s:O = cluster.local
      i:O = cluster.local
    -----BEGIN CERTIFICATE-----
    MIIC3TCCAcWgAwIBAgIQbTtXS7n9A5wEFybaR1XL4jANBgkqhkiG9w0BAQsFADAY
    MRYwFAYDVQQKEw1jbHVzdGVyLmxvY2FsMB4XDTIwMDYxMjE3NTIzMloXDTMwMDYx
    MDE3NTIzMlowGDEWMBQGA1UEChMNY2x1c3Rlci5sb2NhbDCCASIwDQYJKoZIhvcN
    AQEBBQADggEPADCCAQoCggEBAKu9+NueWYbDy7xn5fygMBQydyGGw/6CbIeZ1R06
    ZFFlrKORLt8yvvoe7+JXwrgNe2jvaUkawWQLtsBBrMXewHFkI8g3EsqihTxZ7h3b
    Ii5VEQv1aU9CKxKm1KF9NmcfR1Ucfkho1QdmNGg12b2m7OWC0CpBm1M+7pdR/9Rk
    SVorLbmfzzRtqDXim855MA1JpA9o9oP/QLdEFpZ+NkLCgB4/jjf6KDBsns4kUMIt
    zpZHG1KliVItMwTzntcxwQLE2mrjF0z/7nus6KaGkMJy4QiMd+oCmYQdMY53t0AO
    f9tq4roGL3/PNxTbGe5PDsIkzs0Md8k2sWfqwDw4FlYzrr0CAwEAAaMjMCEwDgYD
    VR0PAQH/BAQDAgIEMA8GA1UdEwEB/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEB
    ABb1HG3PvtPTsphyo/JIGbb+4H4VKjgDCQApY0AjLR1TCgdwwSH54lybQjzRJsn6
    U8jR+iozLzK3ouulP+nWpYMEIU5ZkgL07Lqx0nHhUrBGJuWs+XbTuCL+54u+ka9U
    hCIHbhmP9DOj0CbxqXxSEJyW+Ck+cEItZ5vzxMCUZ1jyide4URveJfsi1z68emUl
    pc6GqglG5i7G3t+kW6zjC9GVLApl1guPX1OP87GaDJYA6YXtLZq8j6CMGaLmMhQ6
    jbo2xrWQSQAvd3tuFY2mIuwtG9t3DLMLz0d98hfzkpKpz1bED1ZuMUb3uGNAihq2
    nRB1i7ol6OIA+b3Pk5P9tps=
    -----END CERTIFICATE-----
    ---
    Server certificate
    subject=

    issuer=O = cluster.local

    ---
    Acceptable client certificate CA names
    O = cluster.local
    Requested Signature Algorithms: ECDSA+SHA256:RSA-PSS+SHA256:RSA+SHA256:ECDSA+SHA384:RSA-PSS+SHA384:RSA+SHA384:RSA-PSS+SHA512:RSA+SHA512:RSA+SHA1
    Shared Requested Signature Algorithms: ECDSA+SHA256:RSA-PSS+SHA256:RSA+SHA256:ECDSA+SHA384:RSA-PSS+SHA384:RSA+SHA384:RSA-PSS+SHA512:RSA+SHA512
    Peer signing digest: SHA256
    Peer signature type: RSA-PSS
    Server Temp Key: X25519, 253 bits
    ---
    SSL handshake has read 2113 bytes and written 431 bytes
    Verification error: self signed certificate in certificate chain
    ---
    New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
    Server public key is 2048 bit
    Secure Renegotiation IS NOT supported
    Compression: NONE
    Expansion: NONE
    No ALPN negotiated
    Early data was not sent
    Verify return code: 19 (self signed certificate in certificate chain)
    ---
    command terminated with exit code 1
    ```

## What's Next

- [ ] Figure out how to integrate with istio as a secret discovery service (SDS).
- [ ] Create a proxy API to translate the SDS calls to Conjur certificate sigining
      using a private key stored in Conjur.

- [ ] Figure out what this snipped actually does:

    ```sh-session
    $kubectl apply -f - <<EOF
      apiVersion: "security.istio.io/v1beta1"
      kind: "PeerAuthentication"
      metadata:
        name: "default"
      spec:
        mtls:
          mode: STRICT
    EOF
    peerauthentication.security.istio.io/default created
    ```