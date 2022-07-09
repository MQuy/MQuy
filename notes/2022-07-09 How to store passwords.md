## How to store passwords

### Salt

**Why we need salt?**
Users can use the password. If for a reason, attackers steals password table, they could try a dictionary attack, using pre-arranged listing of words and it matches after several runs. They can create a map between password and hash -> figuring out other passwords which have the same hash.

In the example below, a attacker figure out hash of `dontpwnme4` is `4420d1918bbcf7686defdf9560bb5087d20076de5f77b7cb4c3b40bf46ec428b`, they can know bob's password.

```
username	hash
alice(dontpwnme4)	4420d1918bbcf7686defdf9560bb5087d20076de5f77b7cb4c3b40bf46ec428b
jason	695ddccd984217fe8d79858dc485b67d66489145afa78e8b27c1451b27cc7a2b
mario	cd5cb49b8b62fb8dca38ff2503798eae71bfb87b0ce3210cf0acac43a3f2883c
teresa	73fb51a0c9be7d988355706b18374e775b18707a8a03f7a61198eefc64b409e8
bob(dontpwnme4)	4420d1918bbcf7686defdf9560bb5087d20076de5f77b7cb4c3b40bf46ec428b
mike	77b177de23f81d37b5b4495046b227befa4546db63cfe6fe541fc4c3cd216eb9
```

By adding salt to password before storing makes sure that each users has their unique password.
![salt](https://i.imgur.com/PTocs7p.png)

### References

- [Adding salt to hashing](https://auth0.com/blog/adding-salt-to-hashing-a-better-way-to-store-passwords/)
