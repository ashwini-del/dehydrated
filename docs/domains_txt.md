## domains.txt

dehydrated uses the file `domains.txt` as configuration for which certificates
should be requested.

The file should have the following format:

```text
example.org
example.com www.example.com
example.net www.example.net wiki.example.net
```

This states that there are the following certificates:
  * `example.org` without any *alternative names*
  * `example.com` with an *alternative name* of `www.example.com`
  * `example.net` with the *alternative names*: `www.example.net` and
    `wiki.example.net`

### Aliases

You can define an *alias* for your certificate which will (instead of the
primary domain) be used as the directory name under your `CERTDIR` and for a
per-certificate lookup. This is done using the `>` character.  This allows
multiple certificates with identical sets of domains but different
configuration to exist.

Here is an example of using an *alias* called `certalias` for creating the
certificate for `example.net` with *alternative names* `www.example.net` and
`wiki.example.net`. The certificate will be stored in the directory `certalias`
under your `CERTDIR`.

```text
example.net www.example.net wiki.example.net > certalias
```

This allows to set per certificates options. The options you can change are
explained in [Per Certificate Config](per-certificate-config.md).

If you want to create different certificate types for the same domain
you can use:

```text
*.service.example.org service.example.org  > star_service_example_org_rsa
*.service.example.org service.example.org  > star_service_example_org_ecdsa
```

Then add a config file `certs/star_service_example_org_rsa/config` with
the value

```
KEY_ALGO="rsa"
```

or respectively

```
KEY_ALGO="ecdsa"
```

### Wildcards

Support for wildcards was added by the ACME v2 protocol.

Certificates with a wildcard domain as the first (or only) name require an
*alias* to be set.  *Aliases* can't start with `*.`.

For example to create the wildcard for `*.service.example.com` your
`domains.txt` could use the *alias* method like this:

```text
*.service.example.com > star_service_example_com
```

This creates a wildcard certificate for only `*.service.example.com` and will
store it in the directory `star_service_example_com` under your `CERTDIR`. As a
note this certificate will **NOT** be valid for `service.example.com` but only
for `*.service.example.com`. So it would, for example, be valid for
`foo.service.example.com`.


Another way to create it is using *alternative names*. For example your
`domains.txt` could do this:

```text
service.example.com *.service.example.com
eggs.example.com *.ham.example.com
```

This creates two certificates one for `service.example.com` with an
*alternative name* of `*.service.example.com` and a second certificate for
`eggs.example.com` with an *alternative name* of `*.ham.example.com`.

**Note:** The first certificate is valid for both `service.example.com` and for
`*.service.example.com` which can be a useful way to create wildcard
certificates.

### Drop-in directory

If a directory named `domains.txt.d` exists in the same location as
`domains.txt`, the contents of `*.txt` files in that directory are appended to
the list of domains, in alphabetical order of the filenames. This is useful for
automation, as it doesn't require editing an existing file to add new domains.

Warning: Behaviour of this might change as the naming between `domains.txt.d`
and the `DOMAINS_D` config variable (which is used for per-certificate
configuration) is a bit confusing.
