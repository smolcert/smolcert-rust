# smolcert-rust

This is the Rust implementation of [smolcert](https://github.com/smolcert/smolcert-spec). In
`smlcrt` you find a simple CLI tool to create (currently only self signed) smolcerts. In
`smolcert-lib` you find a library which allows you to create and validate smolcerts in your rust
projects.

## smolcert-lib

### Usage example

```rust

use smolcert::*;

use ed25519_dalek::{Keypair, PublicKey, Signature};
use rand::rngs::OsRng;

fn correct_format() {
    let mut csprng = OsRng {};
    let keypair: Keypair = Keypair::generate(&mut csprng);
    let extensions: Vec<Extension> = vec![Extension::KeyUsage(KeyUsage::SignCertificate)];

    let mut cert = Certificate::new_self_signed(
        12,
        "connctd self signed".to_string(),
        Validity {
            not_after: 0,
            not_before: 0,
        },
        "subject self".to_string(),
        extensions,
        &keypair,
    )
    .unwrap();
    cert.verify_signature(&keypair.public).unwrap();

    let cert_bytes = cert.to_vec().unwrap();
    ... // Transmit the created smolcert to validate your identity
}
```