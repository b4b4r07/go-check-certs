Notes: I forked because the upstream doesn't seem to be maintained

go-check-certs
==============

This is a simple utility written in Go to check SSL certificates for a list of hosts. Each certificate in the host's certificate chain is checked for the following:

* Expiration date. By default, you will be warned if a certificate will expire within 30 days. This can be adjusted with `-years=X`, `-months=X`, and/or `-days=X`.
* Signature algorithm. Some algorithms have already been sunset, others are in the process of being sunset. This can be spammy, so you can disable the check with `-check-sig-alg=false`.

## Installation

With `go get` command:

```console
$ go get github.com/b4b4r07/go-check-certs
```

## Usage

Usage looks something like:

```console
$ cat ./path/to/file/with/hosts
example.com:443
example.net:443
# This is a comment at the end of a line
example.org
$
$ ./go-check-certs -hosts="./path/to/file/with/hosts"
```

The hosts file is simply a single `hostname:port` per line. Empty lines or lines that start with `#` are ignored.

```console
$ ./go-check-certs
exmpale.com:443^D
```

If no hosts argument is given, it reads input from the stream stdin.

### More

Adding a command to be executed via `slacklog` to cron allows us to detect expiration of certificates in advance:

```cron
00 13 * * * slacklog -- "/usr/bin/go-check-certs -host=/etc/my-hosts -days=30"
```

## Self-signed certificates

go-check-certs is able to validate signature algorithms and expiration dates for self-signed certificates.

Prefix insecure host urls that cannot be verfied against the root CA with "i ".

```
i https://self-signed.example.com
```

go-check-certs will skip verifying `self-signed.example.com`'s cert, but will perform the same signing algorithm and expiration checks on all certs in the bundle. Please be warned that this approach is vulnerable to mitm attacks, as the cert is not verfied against the root CA.

## Current limitations

* A certificate must be valid for it to be checked.

## License

This is licensed under the same terms as the upstream itself.
