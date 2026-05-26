# Liberated SystemD for NixOS!
SystemD Liberated, because unwanted surveillance is bad, actually.

WARNING: USING THIS WILL TRIGGER A FULL SYSTEM REBUILD. THERE IS PROBABLY NO WAY AROUND THIS.

#### *A Forward:*

Firstly, the affected version of systemd that will affect your privacy is v261. Luckily, NixOS is only on v260.1 for now (which you can check [here](https://github.com/NixOS/nixpkgs/blob/nixos-unstable/pkgs/os-specific/linux/systemd/default.nix)). However, that makes this flake harder to test, as patches for systemd to make it work with nixOS seem to be done on a versioning basis and are incompatible with newer versions. If you have a solution, or just want to contribute, please, create a pull request! We would love to have your contributions!

Secondly: please, always check your sources! Don't just download crazy things online! The liberated fork of systemd this will (hopefully!) install onto your system is [here](https://github.com/Jeffrey-Sardina/liberated-systemd), and the source code for systemd itself is [here](https://github.com/systemd/systemd)!

Thirdly, I only have a flake-based approach right now, which is most likely not the best way, but is the way I am most familiar with. An urgent priority is to also have a version that can fit in just configuration.nix. You should be able to just copy-paste an example from the [Official NixOS wiki](https://wiki.nixos.org/wiki/NixOS_Wiki), I just have not done it myself yet.

And finally, systemD itself has not released v261 yet. The plan for systemd-liberated is to mirror release tags from then on out. until then, it is not possible to test the flake base approach either.

## The Flake-Based Approach

In your flake.nix, this will be the planned approach:
```nix

{
  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs?ref=nixos-unstable";

    systemd-liberated = {
      url = "github:Jeffrey-Sardina/liberated-systemd/v259.3";
      flake = false;
    };

    # ...

  };
  outputs = { self, nixpkgs, mysystemd, ... }:
    let
      system = "x86_64-linux";

      systemd-overlay = final: prev: {
        systemd = prev.systemd.overrideAttrs (old: {

          version = "261-liberated";
          src = systemd-liberated;

        });
      };

      pkgs = import nixpkgs {
        inherit system;
        overlays = [ systemd-overlay ];
        #config.allowUnfree = true;
      };
    in
    {
      nixosConfigurations.yourhostname = nixpkgs.lib.nixosSystem {
        
        # ...

        modules = [

          { nixpkgs.pkgs = pkgs; }
  
          ./configuration.nix
  
        ];
      };
    };
}

```

## The Configuration-Based Approach
Coming soon to theaters near you!
