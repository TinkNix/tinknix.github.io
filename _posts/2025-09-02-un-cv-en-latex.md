---
title: Un CV en LaTeX
description: ""
date: 2025-09-02T13:41:32.123Z
preview: ""
tags:
    - Latex
    - IDE
categories: []
layout: post
subtitle: Réaliser votre curriculum vitae as code
author: Damien
comments: false
mathjax: false
draft: true
---

Pour une recherche d'emploi ou pour mettre régulièrement à jour son Curriculum Vitae, de nombreux outils sont à disposition : du classique MS Word à des solutions SaaS comme Canva. Mais avez-vous déjà envisagé de créer votre CV as code ?

Personnellement, j’auto-héberge tous mes services et je suis bien plus à l’aise devant mon IDE (VSCodium) que devant n’importe quel outil graphique. J’adopte autant que possible une approche déclarative pour les configurations et systèmes (OS as code, Infrastructure as Code, SDN, etc.), car c’est, selon moi, la méthode la plus simple, fiable et reproductible pour gérer un environnement.

C’est pourquoi j’ai choisi LaTeX, qui offre une multitude de templates et une grande flexibilité pour concevoir un CV personnalisé, propre et professionnel.

## Installation de LaTeX sous NixOS 25.05

```nix
  # NixOS
  sudo nano /etc/nixos/configuration.nix

    environment.systemPackages = with pkgs; [
      <snip>

      (texlive.withPackages (ps: with ps; [ scheme-full ]))
    ];
```

{: .box-note}
Pour une installation minimale on peut choisir scheme-minimal et déclarer chaque dépendance

```nix
    environment.systemPackages = with pkgs; [
      <snip>

      (texlive.withPackages (ps: with ps; [ scheme-minimal latexmk latex-bin ... ... ]))
    ];
```

Appliquer la configuration
> sudo nixos-rebuild switch

## Choisir un template

Exemples [latextemplates.com](https://www.latextemplates.com/)

## Configuration IDE

Dans VScodium, j'ai installer l'extension LaTeX Workshop
  
Vous pouvez maintenant personnaliser votre CV et le compiler comme un programme. 
