My notes on secure enclave and KeyChain<!--more-->

### Gotchas:
- Secure enclave is great for encrypting cipher texts
- KeyChain is great for saving simple data such as dictionaries
- CryptoKit is a more modern and easy to use kit to deal with secure enclave (although limited in features?)

##### Secure enclave
- ⭐⭐⭐ Really nice priv/pub repo using secure enclave: [https://github.com/agens-no/EllipticCurveKeyPair](https://github.com/agens-no/EllipticCurveKeyPair)  
- Some basic info on secure enclave (bit outdated): [https://github.com/trailofbits/SecureEnclaveCrypto](https://github.com/trailofbits/SecureEnclaveCrypto)
- ⭐ Amazing article (comprehensive + github) [https://medium.com/@alx.gridnev/ios-keychain-using-secure-enclave-stored-keys-8f7c81227f4](https://medium.com/@alx.gridnev/ios-keychain-using-secure-enclave-stored-keys-8f7c81227f4)  
- github resource from the above article: [https://github.com/algrid/keychain-sample](https://github.com/algrid/keychain-sample)  
- and github for the above: [https://github.com/algrid/keychain-sample](https://github.com/algrid/keychain-sample)
- to check secure enclave available: [https://stackoverflow.com/a/49318485](https://stackoverflow.com/a/49318485)
- Some code on secure enclave: [https://www.linkedin.com/pulse/ios-10-how-use-secure-enclave-touch-id-protect-your-keys-satyam-tyagi](https://www.linkedin.com/pulse/ios-10-how-use-secure-enclave-touch-id-protect-your-keys-satyam-tyagi)
- apple secure enclave docs: [https://developer.apple.com/documentation/security/certificate_key_and_trust_services/keys/storing_keys_in_the_secure_enclave](https://developer.apple.com/documentation/security/certificate_key_and_trust_services/keys/storing_keys_in_the_secure_enclave)

##### KeyChain
- using keychain with custom password: [https://medium.com/@alx.gridnev/password-protected-entries-in-ios-keychain-1add5fc97149](https://medium.com/@alx.gridnev/password-protected-entries-in-ios-keychain-1add5fc97149)
- ⭐⭐ Using keychain with biometric authentication: [https://medium.com/@alx.gridnev/biometry-protected-entries-in-ios-keychain-6125e130e0d5](https://medium.com/@alx.gridnev/biometry-protected-entries-in-ios-keychain-6125e130e0d5)
- simple userdefault like keychain wrapper: [https://github.com/jrendel/SwiftKeychainWrapper](https://github.com/jrendel/SwiftKeychainWrapper)
- has biometric support for keychain items: [https://github.com/kishikawakatsumi/KeychainAccess](https://github.com/kishikawakatsumi/KeychainAccess)

##### CryptoKit
- ⭐ "CryptoKit + Secure enclave" basics: [https://www.andyibanez.com/posts/cryptokit-secure-enclave/](https://www.andyibanez.com/posts/cryptokit-secure-enclave/)
- Using cryptokit and enclave: [https://stackoverflow.com/questions/58102399/apple-ios-13-cryptokit-secure-enclave-enforce-biometric-authentication-ahea](https://stackoverflow.com/questions/58102399/apple-ios-13-cryptokit-secure-enclave-enforce-biometric-authentication-ahea)
- Apples cryptokit docs: [https://developer.apple.com/documentation/cryptokit](https://developer.apple.com/documentation/cryptokit)
