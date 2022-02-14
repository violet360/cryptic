original link -> [puzzle](https://gist.github.com/robot-dreams/669c13bc724fdeb9af8460c9b64d5665)

Quick Explaination:
You're given Schnorr signatures on two different messages signed by the same private key. Fortunately for you (the adversary), the signer screwed up their implementation of [BIP-340](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki) and reused a nonce.

Can you capitalize on this fatal error and extract the signer's private key?

Note: You may find it helpful to interpret some of the byte strings as ASCII, in order to check your work.

---

#### Public Key

```
463F9E1F3808CEDF5BB282427ECD1BFE8FC759BC6F65A42C90AA197EFC6F9F26
```

#### Message 1

```
6368616E63656C6C6F72206F6E20746865206272696E6B206F66207365636F6E
```

#### Signature 1

```
F3F148DBF94B1BCAEE1896306141F319729DCCA9451617D4B529EB22C2FB521A32A1DB8D2669A00AFE7BE97AF8C355CCF2B49B9938B9E451A5C231A45993D920
```

#### Message 2

```
6974206D69676874206D616B652073656E7365206A75737420746F2067657420
```

#### Signature 2

```
F3F148DBF94B1BCAEE1896306141F319729DCCA9451617D4B529EB22C2FB521A974240A9A9403996CA01A06A3BC8F0D7B71D87FB510E897FF3EC5BF347E5C5C1
```

Solution:

If you are not familiar with schnorr, better to ask is "what is schnorr?" and "why is schnorr ?".
Here is a simple description:
```
Private Key: PrivKey
Public Key: PubKey = PrivKey*G  (not your traditional multiplication, it is a scalar multiplication, G is the generator point.)
message: m
random cryptographically secure number = k
nonce = k*G (scalar multiplication)
hash function = H(m)

x , y = kG
s = k + H(x || PubKey || H(m))*PrivKey -> signature
(x, s) -> signature tuple
```

How to Verify schnorr Sigs?
```
if s == y + H(x || PubKey || H(m)) * PubKey:
    valid Sig
else:
    Fbi, Open Up!

s = k + H(x || PubKey || H(m))*Priv
s*G = (k + H(x || PubKey || H(m))*PrivKey)*G = k*G + H(x || PubKey || H(m))* PrivKey*G

k*G will generate a point, we only take the y value of it, and PrivKey*G is just PubKey.

s*G = y + H(x || PubKey || H(m)) * PubKey.
```

Let us solve now:
```
s1 = k + H(x || PubKey || H(m<sub>1</sub>))*PrivKey
```