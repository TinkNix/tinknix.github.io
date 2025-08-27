---
title: Niri
description: ""
date: 2025-08-27T11:57:19.306Z
preview: ""
tags:
    - wayland
    - nixos
categories:
    - nixos
layout: post
subtitle: a minimal wayland on nixos
author: Damien
comments: false
mathjax: false
draft: false
---

Depuis des années j'utilisais I3 un Tiling Windows Manager pour X11. J'ai apprécié la simplicité de sa configuration, sa rapidité & légèreté et de plus très utilisé par la communauté. Pour mon passage à wayland, j'ai testé Hyprland, Sway.  
Hyprland trop instable pour l'instant et Sway malgré qu'il soit compatible I3, je n'ai pas accroché.  

J'ai découvert Niri, un scrollable tiling wayland compositor.  
La fonctionnalité "scrollable" est vite adoptée et j'en viens à me demander comment j'avais fait avant. La navigation entre les différentes fenêtres est fluide.  
Il est rapide et la confiuration facile à prendre en main venant de I3.

Niri n'est pas un environnement de bureau complet, ici pas de barre de tâche, pas de gestionnaire de fond d'écran. Minimal, tout ce que j'aime.

## Installation minimale sous Nixos 25.05

> sudo nano /etc/nixos/configuration.nix

```nix
  programs.niri.enable = true;
```

Quelques options pour la compatibilité
```nix
  # Optionnel pour apps sous X11 et Electron
  environment.sessionVariables.NIXOS_OZONE_WL = "1";
  programs.xwayland.enable = true;

  # Optionnel configuration GDM
  services.xserver.displayManager.gdm.enable = true;
  services.displayManager.defaultSession = "niri";

  environment.systemPackages = with pkgs; [
    ...
    # Optionnel pour ajouter une couche X11
    xwayland-satellite
    ...
  ];
```

Appliquer la configfuration
> sudo nixos-rebuild switch

Pour compléter Niri, j'ai rajouté
- rofi-wayland pour le launcher
- rofi-power-menu pour la session
- imv pour le fond d'écran

je n'utilise pas de barre de tâche mais vous en trouverez dans le Awesome Niri

## Ressources

- [Niri](https://github.com/YaLTeR/niri/)
- [Awesome Niri](https://github.com/Vortriz/awesome-niri)
