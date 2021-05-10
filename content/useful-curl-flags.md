---
title: "Useful Curl Flags"
date: 2021-04-06T12:12:24+01:00
tags:
  - tools
draft: true
---

cURL is an exceptionally useful command line HTTP client. With all its flags you can use it
to test a REST API, download files, and even send commands to the Docker daemon over the
Unix socket.

However, with great power comes great complexity. By default, cURL won't follow redirects,
will fail with a 0 exit code, and will happily pump binary output to stdout.

- `-L, --location` - Follow redirects.
- `-k, --insecure` - Don't verify the certificate on connections using SSL. Useful if using a self
signed certificate or an internal Certificate Authority that isn't setup on your system.
- `-o, --ouput <file>` - Write output to file, instead of stdout. Note that without any other flags (`--fail`),
cURL will happily write an error response to that file and not tell you.
- `-f, --fail` - Do not write output on HTTP errors.
- `-s, --silent` - Silent mode. Progress will not be shown.
- `-S, --show-error` - Show error, even when silent mode is used.
- `-x, --proxy <host:port>` - Use the specified proxy. `HTTP_PROXY` and `HTTPS_PROXY` environment variables
can also be used.

## Downloading files

In its simplest form, to download something like an executable, you simply need to do this:

```bash
curl https://example.com/my-file -o /tmp/my-file
```

You'll see a progress bar until the download is complete. Not something that's particularly
helpful in a CI/CD context.

```bash
curl -sS https://example.com/my-file -o /tmp/my-file
```

Here we've suppressed the progress bar, but `-S` will still show any errors that occur.

We still have a problem. By default cURL will happily write errors to the file and return
a `0` exit code.

```bash
curl -sSf https://example.com/my-file -o /tmp/my-file
```

`-f` or `--fail`, will have cURL return a different exit code and the file will not
be created. Perfect for scripts and CI/CD environments.
