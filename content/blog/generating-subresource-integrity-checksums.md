title: Generating Subresource Integrity Checksums
date: 2015-09-23
type: blog
grouping: blog
---
The GitHub Engineering blog recently had a blog post on how they have added [Subresource Integrity](http://githubengineering.com/subresource-integrity/) (SRI) tags on their third party assets. Long story short, this is `integrity` attribute you add to your `<script>` or `<link rel="stylesheet">` tags which point to external (ie. not inlined) assets. Supporting browsers will then check the checksum in the attribute against the file it has downloaded before it parses the file. This is to make sure the file hasn't been tampered with and to avoid the browser loading malicious files because some third party hoster got a security problem.

When I saw the post it was something I wanted to try out. Looking at the post from GitHub it looked like it just was a matter of creating a SHA256 checksum of a file, base64 encode it and then add `integrity="sha256-<base64 encoded checksum>"` to the tag of where the file was loaded. On my Mac it was just a matter of running `shasum --algorithm 256 <file path>` and then encode the checksum with `echo -n <checksum> | base64`. I prepended "sha256-" to the output, stuffed it in the integrity parameter in a HTML file, but when I tried it out in Chrome (Update! Firefox 43 and Opera 32 has since added support for SRI as well), it denied loading the file with the following error message:

> Failed to find a valid digest in the 'integrity' attribute for resource '`<file path>`' with computed SHA-256 integrity '`<base64 encoded checksum>`'. The resource has been blocked.

A couple of questions popped into my head at this time. Why was my base64 encoded checksum double the length of the example provided in the GitHub post, and why was the checksum even base64 encoded? A SHA256 hash only consists of hexadecimal characters, so the extra encoding would only make it take up more space.

In my search for why it didn't work as expected, I found [srihash.org](https://srihash.org/) which is a site that can generate the integrity parameter for you. It did however generate a SHA384 checksum instead, which wasn't directly comparable to what I was testing (and required CORS enabled, which I don't get the reason for), but even their SHA384 checksum which should be longer than my SHA256 checksum, was only 64 characters compared to my 88 characters.

I found the [source code](https://github.com/mozilla/srihash.org) for the website and saw that it depended on [sri-toolbox](https://github.com/neftaly/npm-sri-toolbox) which was the library which generated the checksum. Looking at its source code (a Node.js library) showed this as the function that did the part I was interested in replicating:

    :::javascript
    // Generate hash
    digest = function (algorithm, data) {
        return crypto
            .createHash(algorithm)
            .update(data, 'utf8')
            .digest("base64");
    },

I thought that looked pretty much exactly the same as what I had attempted on the command line, until it finally dawned on me that the generated checksum, before it is base64 encoded, actually is a binary blob. I have always thought of a checksum as a string of hexadecimal digits as that is how it's normally represented in checksum files and in the output from tools that can generate and check checksums. That is however just a representation of the "low level" checksum, which explains why the checksum is base64 encoded according to the SRI specifications. With this new found knowledge, I was then able to generate a correct checksum in Python that Chrome would accept.

In attempt to help out other people who may want to make use of this new technology in order to secure their users from malicious scripts, I'll try to collect some examples of how to generate the checksums needed in some different environments:


Shell
-----

    :::shell
    echo -n sha256-; \
        cat <input file> | \
        openssl dgst -sha256 -binary | \
        base64


Python
------

    :::python
    import base64
    import hashlib

    def checksum(input):
        if isinstance(input, str):
            # This is so we can handle strings as input in Python 3
            input = input.encode()

        hash = hashlib.sha256(input).digest()
        hash_base64 = base64.b64encode(hash).decode()
        return 'sha256-{}'.format(hash_base64)


Node.js
-------

    :::javascript
    var crypto = require('crypto');

    function checksum (input) {
        var hash = crypto.createHash('sha256').update(input, 'utf8');
        var hashBase64 = hash.digest('base64');
        return 'sha256-' + hashBase64;
    }


Go
--

    :::go
    import (
        "crypto/sha256"
        "encoding/base64"
    )

    func Checksum(input []byte) string {
        hash := sha256.Sum256(input)
        hash_base64 := base64.StdEncoding.EncodeToString(hash[:])
        return "sha256-" + hash_base64
    }


Ruby
----

    :::ruby
    require 'digest'
    require 'base64'

    def checksum(input)
        hash = Digest::SHA256.digest input
        hash_base64 = Base64.encode64(hash)
        return "sha256-#{hash_base64}"
    end


PHP
---

    :::php
    <?php
    function checksum($input) {
        $hash = hash('sha256', $input, true);
        $hash_base64 = base64_encode($hash);
        return "sha256-$hash_base64";
    }
    ?>


I hope this was useful to you. If you want to contribute examples in other languages, then please [let me know](https://twitter.com/Tenzer) or submit a pull request to the [repository](https://github.com/Tenzer/tenzer.dk) for my website.
