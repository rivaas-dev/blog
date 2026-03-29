---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
date: {{ .Date }}
description: ""
author: ""
tags: []
keywords: []
draft: true
resources:
  - src: "**.{png,jpg}"
    title: "Image #:counter"
---

Add a **featured.png** (or any `feature*` / `cover*` / `thumbnail*` image) in this folder for card thumbnails and hero — see [Blowfish thumbnails](https://blowfish.page/docs/thumbnails/).
