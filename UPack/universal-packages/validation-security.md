---
title: Validation & Security
sequence: 30
show-headings-in-nav: true
---
A Universal Package is intended to be read-only, and once created, its contents and metacontents sealed within the package, untampered. However, the simple format of a Universal Package makes it easy to tamper with its contents using nothing more than a zip file editor.

This is where cryptographic hashing comes in. It is a small string of text that acts as a "thumbprint" of a file and lets you verify that, after you've downloaded "Accounts/HDars v1.3.4" from a package source, you can be certain it's the file you expect.

Because a package's hash is calculated from the bytes of the package file, it is impossible to store a package's hash inside of that package, since changing the package would change its hash. For this reason, a trusted package source should be used to verify the hash of the package.

However, a package [manifest file](manifest) may reference other packages' hashes in the `dependencies` and `repackageHistory` properties. 

### Secure Package Identifier{#secure-id data-title="Secure Package Identifier"}
A universal package can be uniquely identified buy it's group, name, and version. In some cases, such as when specifying a dependency or describing an audit trail, these will all be combined in a single string.

The format for this string is fairly simple: the group and name are combined (separated by a forward-slash), and the version is appended (separated by a colon). For example:

- ```HDARS:1.3.9```
- ```initrode/vendors/abl/ABLast:2.2.1-rc.1```

To ensure a tamper-proof chain of packages, you may also use a package's cryptographic hash by appending a colon and then a hash string to the end of a package. 

This will be used for verification purposes when possible. For example:

- ```HDARS:1.3.9:fca66ce2a8ceea2d651eecf2369d4072d1871aec```
- ```initrode/vendors/abl/ABLast:2.2.1-rc.1:5b31eaa26d0c6e7bb985f740dbceed854293c369```

If the identified packages exist, but the hash doesn't exist, then the package will not be used.

### Package Hash Format{#package-hash-format data-title="Package Hash Format"}

Package hashes are calculated using the SHA-1 algorithm, and encoded visually as a 40-character case-insensitive string of hexidemical digits without spaces or other separator characters. For example: `2660bf74fc8147ca41bd53bdb1defc3aae35bc91`


### Learn More
<iframe width="600" height="337" src="https://www.youtube.com/embed/xf29CZK3O6s" frameborder="0" allowfullscreen="true"></iframe>


<!--
You can use the following rules to determine how to validate a package hash string against a file, or generate a hash of your own package.
<ul>
    <li>40 characters - sha-1</li>
    <li>64 characters - sha-256</li>
    <li>128 characters - sha-512</li>
    <li>73 characters and beginning with "SHA3-256:" - SHA3-256, beginning after the colon</li>
    <li>137 characters and beginning with "SHA3-512:" - SHA3-512, beginning after the colon</li>
    <li>other - invalid or unknown hash</li>
</ul>
-->