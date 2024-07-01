<h1 align="center">uthoctl</h1>


uthoctl is the command line client to access the [utho-api](https://utho.com/api-docs) by utlizing the utho-go SDK [utho-sdk](https://github.com/uthoplatforms/utho-go).

- [Installing `uthoctl`](#installing-uthoctl)
  - [Downloading a Release from GitHub](#downloading-a-release-from-github)
  
- [Examples](#examples)


## Installing `uthoctl`

### Downloading a Release from GitHub

Visit the [Releases
page](https://github.com/uthoplatforms/utho-cli/releases) for the
[`uthoctl` GitHub project](https://github.com/uthoplatforms/utho-cli), and find the
appropriate archive for your operating system and architecture.
Download the archive from your browser or copy its URL and
retrieve it to your home directory with `wget` or `curl`.

For example, with `wget`:

```
cd ~
wget https://github.com/uthoplatforms/utho-cli/releases/download/v<version>/uthoctl_<version>_linux_amd64.tar.gz
```

Or with `curl`:

```
cd ~
curl -OL https://github.com/uthoplatforms/utho-cli/releases/download/v<version>/uthoctl_<version>_linux_amd64.tar.gz
```

Extract the binary:

```
tar xf ~/uthoctl-<version>-linux-amd64.tar.gz
```

Or download and extract with this oneliner:
```
curl -sL https://github.com/uthoplatforms/utho-cli/releases/download/v<version>/uthoctl_<version>_linux_amd64.tar.gz | tar -xzv
```

where `<version>` is the full semantic version, e.g., `0.14.0`.

On Windows systems, you should be able to double-click the zip archive to extract the `uthoctl` executable.

Move the `uthoctl` binary to somewhere in your path. For example, on GNU/Linux and OS X systems:

```
sudo mv ~/uthoctl /usr/local/bin
```

Windows users can follow [How to: Add Tool Locations to the PATH Environment Variable](https://msdn.microsoft.com/en-us/library/office/ee537574(v=office.14).aspx) in order to add `uthoctl` to their `PATH`.

## Authenticating with Utho

To use `uthoctl`, you need to authenticate with Utho by providing a Personal Access Token (PAT) is the only method of
authenticating with the API. You can manage your tokens
at the Utho Control Panel [Applications Page](https://console.utho.com/api).

```
uthoctl auth
```

You will be prompted to enter the Utho access token that you generated in the Utho control panel.

## Examples

`uthoctl` is able to interact with your Utho resources. Below are a few common usage examples.

* List all Compute Instances on your account:
```
uthoctl instance list
```

* Add new domain tp your account:
```
uthoctl domain <domain-name>
```

* Get information about your account:
```
uthoctl account get
```
