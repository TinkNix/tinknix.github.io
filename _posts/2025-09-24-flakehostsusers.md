---
title: NixOS Flake pour plusieurs utilisateurs et configurations
description: ""
date: 2025-09-24T12:42:58.502Z
preview: ""
tags:
  - nixos
  - distribution
categories: []
layout: post
subtitle: Configuration multiples utilisateurs et hôtes
author: Damien
comments: false
mathjax: false
draft: false
---

Dans l'article [précédent](https://tinknix.github.io/2025-09-06-flake/), nous avons vu comment migrer une configuration NixOS vers l’utilisation de Flake combiné à Home Manager. Cette approche concernait un seul utilisateur avec une configuration spécifique. Nous allons désormais aborder comment gérer plusieurs utilisateurs ainsi que plusieurs configurations matérielles dans un seul flake pour une gestion centralisée.

## Evolution de la structure

La structure devra évoluer pour prendre en charge la gestion de plusieurs utilisateurs ainsi que différentes configurations matérielles. Il n’existe pas de modèle standardisé, mais la plupart des configurations adoptent l’usage d’un répertoire dédié, souvent nommé « hosts », pour organiser ces éléments.

Je propose:

```nix
# Avant
.
├── flake.lock
├── flake.nix
├── home-manager
│   └── home.nix
├── modules
│   ├── home-manager
│   │   └── default.nix
│   └── nixos
│       └── default.nix
├── nixos
│   ├── configuration.nix
│   └── hardware-configuration.nix
├── overlays
│   └── default.nix
└── pkgs
    └── default.nix

# Après
.
├── flake.lock
├── flake.nix
├── home-manager
│   ├── home_utilisateur_1.nix
│   └── home_utilisateur_2.nix
├── hosts
│   ├── desktop
│   │   ├── configuration.nix
│   │   └── hardware-configuration.nix
│   └── laptop
│       ├── configuration.nix
│       └── hardware-configuration.nix
├── modules
│   ├── home-manager
│   │   ├── default.nix
│   └── nixos
│       ├── default.nix
├── overlays
│   └── default.nix
└── pkgs
    └── default.nix
```

```sh
mkdir -p hosts/{desktop,laptop}
mv nixos/* hosts/desktop/
rm -r nixos
mv home-manager/home.nix home-manager/home_utilisateur_1.nix
```

Flake.nix devra être modifié pour déclarer cette nouvelle structure.

Section nixosConfigurations, exemple:

```nix
      nixosConfigurations = {

        desktop = nixpkgs.lib.nixosSystem {
          specialArgs = { inherit inputs outputs; };
          modules = [ ./hosts/desktop/configuration.nix ];
        };

        laptop = nixpkgs.lib.nixosSystem {
          specialArgs = { inherit inputs outputs; };
          modules = [ ./hosts/laptop/configuration.nix ];
        };
      };
```

Section homeConfigurations, exemple:

```nix
      homeConfigurations = {

        "utilisateur1@desktop" = home-manager.lib.homeManagerConfiguration {
          pkgs =
            nixpkgs.legacyPackages.x86_64-linux;
          extraSpecialArgs = { inherit inputs outputs; };
          modules = [ ./home-manager/home_utilisateur_1.nix ];
        };

        "utilisateur2@dlaptop" = home-manager.lib.homeManagerConfiguration {
          pkgs =
            nixpkgs.legacyPackages.x86_64-linux;
          extraSpecialArgs = { inherit inputs outputs; };
          modules = [ ./home-manager/home_utilisateur_2.nix ];
        };
      };
```

### Appliquer la nouvelle configuration

Dans votre répertoire de travail

```nix
# Sur PC desktop
sudo nixos-rebuild switch --flake .#desktop
home-manager switch --flake .#utilisateur1@desktop

# Sur PC laptop
sudo nixos-rebuild switch --flake .#laptop
home-manager switch --flake .#utilisateur2@laptop
```

### Note

Cette structure peut être utilisée pour gérer plusieurs profils utilisateur au sein d’une même configuration matérielle. Par exemple, il est possible de définir plusieurs profils distincts selon les besoins :
- Mode développement
- Mode jeu
- Mode montage studio

Associée à un système de gestion de version comme Git, cette organisation centralisée permet également de déployer facilement les configurations vers des machines distantes. Les possibilités offertes par cette approche sont vastes et s’adaptent à de nombreux cas d’usage.
  
