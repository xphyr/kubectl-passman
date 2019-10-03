# kubectl user password manager glue

![CI status badge](https://github.com/chrisns/kubectl-passman/workflows/CI%20Pipeline/badge.svg) 
![LICENSE](https://img.shields.io/github/license/chrisns/kubectl-passman) 
![GitHub watchers](https://img.shields.io/github/watchers/chrisns/kubectl-passman?style) 
![GitHub stars](https://img.shields.io/github/stars/chrisns/kubectl-passman) 
![GitHub forks](https://img.shields.io/github/forks/chrisns/kubectl-passman) 
![GitHub issues](https://img.shields.io/github/issues-raw/chrisns/kubectl-passman) 
![GitHub closed issues](https://img.shields.io/github/issues-closed-raw/chrisns/kubectl-passman) 
![GitHub pull requests](https://img.shields.io/github/issues-pr-raw/chrisns/kubectl-passman) 
![GitHub closed pull requests](https://img.shields.io/github/issues-pr-closed-raw/chrisns/kubectl-passman) 
![GitHub repo size](https://img.shields.io/github/repo-size/chrisns/kubectl-passman) 
![GitHub contributors](https://img.shields.io/github/contributors/chrisns/kubectl-passman)
![GitHub last commit](https://img.shields.io/github/last-commit/chrisns/kubectl-passman)

 > :heavy_exclamation_mark: An easy way to store your kubernetes credentials in [1password](https://1password.com/) or [Mac OS Keychain](https://support.apple.com/en-gb/guide/keychain-access/kyca1083/mac) (more password managers coming soon)

### Does your `~/.kube/config` look like this:

```yaml
apiVersion: v1
kind: Config
users:
- name: my-prod-user
  user:
    token: <REAL TOKEN!>
- name: docker-desktop
  user:
    client-certificate-data: <REAL CERT!>
    client-key-data: <REAL PRIVATE KEY!>
```

## :scream: :scream: :scream: :scream:<br/><br/>Do you scold your parents :man_teacher:/:woman_teacher: for maintaining a `passwords.doc` on their desktop? <br/><br/> Then you need kubectl-passman!

> a suggestion of a better name is very welcome, it's not too late to change!

What this `kubectl` [plugin](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#client-go-credential-plugins) does is glue your kubectl config to a common password manager.

## Installation

```bash
go install github.com/chrisns/kubectl-passman
```

## Usage

### Shared 

You need to JSON encode the credentials so that should look something like:

```json
{"token":"some-token"}
```

or for a key pair:

```json
{
  "clientCertificateData":"-----BEGIN REAL CERTIFICATE-----\nMIIC9DCCA.......-----END CERTIFICATE-----",
  "clientKeyData":"-----BEGIN REAL RSA PRIVATE KEY-----\nMIIE......-----END REAL RSA PRIVATE KEY-----"
}
```

### macOS Keychain

You then place the json encoded string in a keychain item, call it whatever you like but keep the account name and item name the same.

#### Keypair

![Screenshot of adding a keypair](resources/osxkeychain-keypair.png)

#### Token

![Screenshot of adding a token](resources/osxkeychain-token.png)

Then add it to the `~/.kube/config`:

```yaml
apiVersion: v1
kind: Config
users:
- name: my-prod-user
    user:
      exec:
        command: "kubectl-passman"
        apiVersion: "client.authentication.k8s.io/v1beta1"
        args:
          - keychain
          - kubectl-prod-user
```

### 1Password

You will need the [1Password commandline client installed and configured](https://1password.com/downloads/command-line/). I'm really not sure on how to best handle the session token, storing it in your `~/.kube/config` would probably be even worse than the secret to start with.

You then place the json encoded string in a 1password login item, call it whatever you like

#### Keypair

![Screenshot of adding a keypair](resources/1password-keypair.png)

#### Token

![Screenshot of adding a token](resources/1password-token.png)

Then add it to the `~/.kube/config`:

```yaml
apiVersion: v1
kind: Config
users:
- name: my-prod-user
    user:
      exec:
        command: "kubectl-passman"
        apiVersion: "client.authentication.k8s.io/v1beta1"
        args:
          - "1password"
          - kubectl-prod-user
```

## Compiling

``` bash
go build
```

## Contributing

I :heart: contributions, it'd be great if you could add support for your favourite password manager, work on something from the [TODO](#TODO) or any open issues as a priority, but anything else that takes your fancy too is great, though best to raise an issue to discuss before investing time into it.
