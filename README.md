# nix-update-action

This action uses `nix-update` to update flake packages.

Heavily inspired by [update-flake-lock](https://github.com/DeterminateSystems/update-flake-lock).

## Examples

There are several examples of how to use this workflow to update flake packages.

### Update all packages

To update all packages in flake you may use this workflow:

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        uses: winapps-org/nix-update-action@v1.3.0
```

### Update specific packages

It's possible to update only certain packages by specifying them in `packages` variable in a comma-separated list

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        uses: winapps-org/nix-update-action@v1.3.0
        with:
          packages: "geth,besu"
```

### Update all packages except blacklisted

We also can blacklist some packages in updates:

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        uses: winapps-org/nix-update-action@v1.3.0
        with:
          blacklist: "teku,lighthouse"
```

### Print the number of the created PR

To print the number of the created PR you can use this workflow:

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        id: update
        uses: winapps-org/nix-update-action@v1.3.0
      - name: Print PR number
        run: echo Pull request number is ${{ steps.update.outputs.pull-request-number }}.
```

### Use a different Git user

To modify author and/or commiter you can do:

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        id: update
        uses: winapps-org/nix-update-action@v1.3.0
        with:
          git-author-name: 'John Author'
          git-author-email: 'github-actions[bot]@users.noreply.github.com'
          git-committer-name: 'John Committer'
          git-committer-email: 'github-actions[bot]@users.noreply.github.com'
```

### GPG commit signing

It's possible for the bot to produce GPG signed commits. Associating a GPG public key to a github user account is not required but it is necessary if you want the signed commits to appear as verified in Github. This can be a compliance requirement in some cases.

You can follow [Github's guide on creating and/or adding a new GPG key to an user account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-new-gpg-key-to-your-github-account). Using a specific github user account for the bot can be a good security measure to dissociate this bot's actions and commits from your personal github account.

For the bot to produce signed commits, you will have to provide the GPG private keys to this action's input parameters. You can safely do that with [Github secrets as explained here](https://github.com/crazy-max/ghaction-import-gpg#prerequisites).

When using commit signing, the commit author name and email for the commits produced by this bot would correspond to the ones associated to the GPG Public Key.

If you want to sign using a subkey, you must specify the subkey fingerprint using the `gpg-fingerprint` input parameter.

You can find an example of how to using this action with commit signing below:

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        id: update
        uses: winapps-org/nix-update-action@v1.3.0
        with:
          sign-commits: true
          gpg-private-key: ${{ secrets.GPG_PRIVATE_KEY }}
          gpg-passphrase: ${{ secrets.GPG_PASSPHRASE }}
          gpg-fingerprint: ${{ secrets.GPG_FINGERPRINT }} # specify subkey fingerprint (optional)
```

### Use assignees or reviewers

To request a review in PR you can use `pr-assignees` and `pr-reviewers` like that:

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        id: update
        uses: winapps-org/nix-update-action@v1.3.0
        with:
          pr-assignees: User1
          pr-reviewers: User2,User3
```

### Pass extra arguments directly to nix-update

If extra arguments need to be passed to `nix-update` (like for example `--version=branch`) use `extra-args` like:  

```yaml
name: "Update Flake Packages ❄️"
on:
  workflow_dispatch:
  schedule:
    - cron: "0 10 * * 0" # https://crontab.guru/#0_10_*_*_0
jobs:
  updateFlakePackages:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
      - name: Install Nix
        uses: cachix/install-nix-action@v27
      - name: Update flake packages
        id: update
        uses: winapps-org/nix-update-action@v1.3.0
        with:
          extra-args: --version=branch
```

Note that these arguments will be passed after the default `--flake --commit`, which cannot be overridden.  
