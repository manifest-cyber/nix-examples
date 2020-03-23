# nix-examples (Work In Progress!)

This repository serves as comprehesive list of Nix examples for various technologies. The main goal of these examples is to be as simple as possible, has the same interface and demonstrate possibilities of Nix. Note that examples are created only with official tools available in Nixpkgs. Some examples could be done better with unofficial tools, but using the most effecient solution at any cost is not purpose of these examples.

Each project contains from five to nine `.nix` files:

* `nixpkgs.nix` -- pin of Nixpkgs and its configuration and overlays
* `app.nix` -- app derivation (can be easily integrated into Nixpkgs tree or own packages tree)
* `default.nix` -- calls `app.nix` with pinned Nixpkgs
* `shell.nix` -- similar to `default.nix`, but overriding some attributes or adding some dev tools
* `ci.nix` -- set of available CI/CD jobs
* `module.nix` -- app as NixOS module (can be easily integrated into NixOS modules tree or own modules tree)
* `cd.nix` -- logical deployment specification
* `cd-*.nix` -- physical deployment specification (for example `cd-vbox.nix` describe deployment to VirtualBox machines)

There is template for new example, which contains only these files with some additional info.

## List of example projects and its sources

- desktop
    - C:
        - Autoools -- GNU hello -- https://www.gnu.org/software/hello/
    - C#:
        - TODO
    - C++:
        - Autoools -- TODO
    - Go:
        - Go Modules -- hello -- https://github.com/golang/go/wiki/Modules
    - Haskell:
        - Cabal -- answer -- https://github.com/shajra/example-nix
        - Cabal -- answer-generated -- https://github.com/shajra/example-nix
    - Java:
        - Ant -- AntDateUtils -- https://www.mkyong.com/ant/ant-how-to-create-a-java-project/
        - Maven -- Spring boot -- https://start.spring.io/
        - Gradle -- gs-gradle -- https://github.com/spring-guides/gs-gradle
    - JavaScript:
        - NPM -- cowsay-1.4.0 -- https://github.com/piuccio/cowsay
        - Yarn -- TODO
    - PHP:
        - Composer -- hello-pdf -- https://github.com/svanderburg/composer2nix
        - Composer -- laravel-cli
    - Python:
        -- TODO maybe https://github.com/manolomartinez/greg
    - Ruby:
        -- TODO

- distributed
    - TODO

- mobile
    - Android:
        - Gradle -- Android CardView Sample -- https://github.com/android/views-widgets-samples/tree/master/CardView/
        - Ant -- myfirstapp -- https://github.com/svanderburg/nix-androidenvtests

- multitier
    - TODO

- web
    - Laravel

TODO what next? Maybe TypeScript, Rust, Kotlin, Scala, Clojure, Elixir, Dart, WebAssembly, Erlang, F#, ...

### Project dependencies

There are three was to get dependencies when using Nix:

 1. Dependecies can be easily specified by its name in Nix file (dependencies in Nixpkgs).
 2. Dependecies derivations can be generated from some file. For example for NPM you can use `node2nix`.
 3. All dependecies must be treated as one single derivation (Fixed Output Derivation).

Overview of available ways of getting dependencies in examples by dependency tool is in following table:

| Dependency tool  | dependencies in Nixpkgs | dependencies derivations generated | dependencies as one derivation (FOD) |
|-------------     |:-----------------------:|:----------------------------------:|:------------------------------------:|
| Autotools        | :heavy_check_mark:      | --                                 | --                                   |
| Go modules       | --                      | --                                 | :heavy_check_mark:                   |
| Cabal            | :heavy_check_mark:      | --                                 | --                                   |
| Ant              | --                      | --                                 | --                                   |
| Maven            | --                      | --                                 | :heavy_check_mark:                   |
| Gradle           | --                      | --                                 | :heavy_check_mark:                   |
| NPM              | --                      | :heavy_check_mark:                 | :heavy_check_mark:                   |
| Composer         | --                      | --                                 | :heavy_check_mark:                   |

## Notes

 * This repo contains source code of common public projects as is. Source files are included in repo only for convenience. If you think that it's violating some license rules please let me know. But keep in mind that this is not real project, included source files are not used for commercial reasons.

 * Some source code of projects had to be a little bit modified. For example JavaScript cowsay repo does not contain `package-lock.json`, so this file was added. See section 'Example specific notes' below for other changes.

 * Each example is self contained and does not have dependency on other files out of its folder. So a lot of code is repeating but it is intentional.

 * In Nix, arguments of package are not distinguished. The examples follow convention that, dependencies are listed first, and then the other arguments.

 * Each project has boolean arg `localFiles`, which switches between sources from remote repository and local files. But most of the projects do not have own repository, so remote sources fetching will not work.

 * There is copy of `nixpkgs-19.09.tar.gz` included in repo. It is to keep examples working even if Nixpkgs will be completely changed in future.

 * This project was created as part of my thesis at Brno University of Technology.

 * Any help or feedback is really appreciated.

### Example specific notes:

- `desktop/JavaScript/cowsay-1.0.4`
    - Project does not contain `package-lock.json`, so it was added.
    - In `package.json` was removed `rollup -c` from prepublish script.

- `desktop/JavaScript/cowsay-1.0.4-fod`
    - Project does not contain `package-lock.json`, so it was added.
    - In `package.json` was removed `rollup -c` from prepublish script.

- `desktop/PHP/hello-pdf`
    - Composer stores some autoloading info in `vendor` directory. For this reason, dependencies must be getting as FOD.

- `desktop/PHP/laravel-cli`
    - Composer stores some autoloading info in `vendor` directory. For this reason, depenedcies must be getted as FOD.
    - Files in `database` must be present when getting dependencies.

- `mobile/Android/CardView`
    - To use Android SDK you must set `android_sdk.accept_license = true;` in Nixpkgs config.

- `mobile/Android/myfirstapp`
    - To use Android SDK you must set `android_sdk.accept_license = true;` in Nixpkgs config.

- `web/Laravel`
    - When deploying Laravel web apps, there is problem with storage path. Unfortunately in Laravel it is not easy to set storage path via `.env`. Changing of `storage_path` in config is not enough, because storage path must be set before config is loaded at all. So you must create `app/Foundation/Application.php` class which extends original Laravel `Application.php` class, in which you change `storage_path`. Next you set new `Application.php` in `bootstrap/app.php` and finnally you can specify `APP_STORAGE_PATH` in `.env`.
    - If local database is used, then user is authenticated via socket authentication. For this reason, in `.env.example` file variable `DB_SOCKET` was added.

## Common commands

If you do not have Nix installed, you can use Docker to build image:
```bash
docker build -t nix/my-hello:dev . -f Dockerfile
```

Create container and detach. The project files and Nixpkgs must be mounted (if you don't have Nixpkgs just clone Nixpkgs repository and change path in command below):
```bash
docker run --detach \
    --name nix-my-hello \
    --mount type=bind,source="$(pwd)",target=/mnt \
    # only if Nixpkgs are not in container
    #--mount type=bind,source=$HOME/.nix-defexpr/channels/nixpkgs,target=/nix/var/nix/profiles/per-user/root/channels/nixpkgs \
    -t nix/my-hello:dev
```

To get into container run:
```bash
docker exec -it nix-my-hello bash
```

Every Nix related command written below can be executed in container by (executed in `/mnt` dir):
```bash
docker exec nix-my-hello <command>
```

For interactive shell specify the `-i` argument:
```bash
docker exec -it nix-my-hello nix-shell --pure
```

Or create container, run command and clean up:
```bash
docker run --rm \
    --name nix-my-hello \
    --mount type=bind,source="$(pwd)",target=/mnt \
    # only if nixpkgs are not in container
    #--mount type=bind,source=$HOME/.nix-defexpr/channels/nixpkgs,target=/nix/var/nix/profiles/per-user/root/channels/nixpkgs \
    -it nix/my-hello:dev <command>
```

This will start an interactive shell in which all environment variables defined by the derivation path have been set to their corresponding values, and the script `$stdenv/setup` has been sourced, so functions from `setup.sh` can be used in shell:
```bash
nix-shell --pure
```

If you customized your `.bashrc`, then you might have some error when running `nix-shell --pure`. It is recommended to stop evaluating `.bashrc` when in `nix-shell`, by adding this line at the begining of `.bashrc`:
```bash
if [[ -n $IN_NIX_SHELL ]]; then return; fi
```

If the derivation defines the atribute `shellHook`, it will be evaluated after `$stdenv/setup` has been sourced. Since this hook is not executed by regular Nix builds, it allows you to perform initialisation specific to `nix-shell`:
```nix
shellHook = ''
  echo "Hello shell"
'';
```

Execute the command in a non-interactive shell:
```bash
nix-shell --run cmd
nix run --command cmd
```

Build app and create result symlink, which is registered as GC root (note that when you are using `nix-shell`, then GC root is not created):
```bash
nix-build
```

Install app dependencies into user environment:
```bash
nix-env -f default.nix -i -A 'buildInputs'
```

Install app into user environment:
```bash
nix-env -f default.nix -i
```

Overlays for Nixpkgs can be defined in `~/.config/nixpkgs/overlays`:
```nix
self: super:
  {
    my-app = super.callPackage ./pkgs/my-app { };
  }
```

Export closure:
```bash
nix-store --export $(nix-store -qR $(type -p my-app)) > my-app.closure
```

Import closure (user exceuting this command must be trusted or closure must be signed):
```bash
nix-store --import < my-app.closure
```

Create closure NAR:
```bash
nix-store --dump path
```

Restore closure NAR archive to path:
```bash
nix-store --restore path
```

Install imported output path (`.drv` files are not in closure):
```bash
nix-env -i /nix/store/hash-my-hello-1.0
```

Copy clousre to remote (non-official `nix-deploy` utility can be also used):
```bash
nix-copy-closure --to alice@itchy.example.org $(type -p my-app)
```

Command `nix-copy-closure` will be replaced with `nix` command in future:
```bash
nix copy --to ssh://alice@itchy.example.org $(type -p my-app)
```

To copy Firefox from the local store to a binary cache in `file:///tmp/cache`:
```bash
nix copy --to file:///tmp/cache $(type -p firefox)
```

To copy the entire current NixOS system closure to another machine via SSH:
```bash
nix copy --to ssh://server /run/current-system
```

To copy a closure from another machine via SSH:
```bash
nix copy --from ssh://server /nix/store/a6cnl93nk1wxnq84brbbwr6hxw9gp2w9-blender-2.79-rc2
```

Show all path dependencies:
```bash
nix-store --query --tree /nix/store/hash-my-hello-1.0
```

Show only path references (first level dependencies):
```bash
nix-store --query --references /nix/store/hash-my-hello-1.0
```

Show derivation:
```bash
nix show-derivation /nix/store/hash-my-hello-1.0.drv
```

Run CI job:
```bash
nix-build ci.nix -A build --no-out-link
```

On CI server we can set number of build jobs by `--max-jobs` argument (maximum of building derivation jobs in parallel):
```bash
nix-build ci.nix -A pipeline --no-out-link --max-jobs auto
```

TODO NixOS commands

Each machine configuration can be generated in different formats with `nix-community/nixos-generators`.

Create deployment (existing NixOS installation or VirtualBox):
```bash
nixops create ./cd.nix ./cd-nixos.nix -d example
nixops create ./cd.nix ./cd-vbox.nix -d example-vbox
```

Apply deployment:
```bash
nixops deploy --force-reboot -d example
```

SSH into machine:
```bash
nixops ssh -d example server
```

Destroy deployment:
```bash
nixops destroy -d example
```

Delete deployment:
```bash
nixops delete -d example
```

## License

Each example project may has own licence. All other things in this repo are licensed under GNU/GPL:

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program. If not, see <http://www.gnu.org/licenses/>.

