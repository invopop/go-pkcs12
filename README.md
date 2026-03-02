# go-pkcs12 (Invopop fork)

Fork of [SSLMate/go-pkcs12](https://github.com/SSLMate/go-pkcs12) with tolerant certificate parsing.

## Why this fork exists

Go's `crypto/x509` parser rejects certificates that use BIT STRING (ASN.1 tag 3) or OCTET STRING (tag 4) in Distinguished Name attributes. This is valid per the X.520 spec — for example, `x500UniqueIdentifier` (OID 2.5.4.45) is defined as BIT STRING. OpenSSL parses these certificates fine, but Go does not.

The root cause is in `crypto/x509` ([Go issue #48371](https://github.com/golang/go/issues/48371), open since 2021, marked Unplanned). A proposal for lenient parsing was explicitly rejected by the Go team. No alternative Go x509 library (zcrypto, certificate-transparency-go) handles this either.

Since go-pkcs12's `DecodeChain` calls `x509.ParseCertificates` internally with no extension point, forking is the only way to add tolerance without restructuring the consuming application.

## What changed

Two `x509.ParseCertificates` calls in `pkcs12.go` (`DecodeChain` and `DecodeTrustStore`) are replaced with [`x509tolerant.ParseCertificates`](https://github.com/invopop/x509tolerant), which handles the DER-patching fallback. No other code is modified.

## Upstream

Original: https://github.com/SSLMate/go-pkcs12
