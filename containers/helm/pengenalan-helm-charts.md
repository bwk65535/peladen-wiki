---
title: Pengenalan Helm Charts
description: 
published: true
date: 2025-01-02T08:16:46.884Z
tags: containers, helm
editor: markdown
dateCreated: 2025-01-02T08:16:46.884Z
---

Helm merupakan salah satu automation tools yang bersifat *declarative*. Artinya kita sebagai operator cukup mendefinisikan *end state* atau hasil akhir yang diinginkan, lalu helm akan berusaha menjalankan step-step untuk mencapai end state tersebut. Definisi *end state* yang dijalankan helm akan kita tuliskan dalam bentuk **helm charts**.

Di dalam helm charts, file-file manfiest kubernetes yang dibutuhkan untuk deployment akan dibuat dalam bentuk *template*. Values yang dibutuhkan dalam template dapat kita ambil dari file lain yang umumnya diberi nama **values.yaml**. Nilai dari values.yaml inilah yang akan di-apply ke dalam template sehingga menjadi file deployment kubernetes yang utuh.

Berikut contoh struktur dari helm charts:
```
wordpress/
  Chart.yaml          # A YAML file containing information about the chart
  LICENSE             # OPTIONAL: A plain text file containing the license for the chart
  README.md           # OPTIONAL: A human-readable README file
  values.yaml         # The default configuration values for this chart
  values.schema.json  # OPTIONAL: A JSON Schema for imposing a structure on the values.yaml file
  charts/             # A directory containing any charts upon which this chart depends.
  crds/               # Custom Resource Definitions
  templates/          # A directory of templates that, when combined with values,
                      # will generate valid Kubernetes manifest files.
  templates/NOTES.txt # OPTIONAL: A plain text file containing short usage notes
```

## Chart.yaml
Selain kedua jenis file tersebut, ada juga file bernama **Chart.yaml** yang berisi informasi mengenai helm chart tersebut.

Berikut contoh isi dari Chart.yaml:
```
apiVersion: The chart API version (required)
name: The name of the chart (required)
version: A SemVer 2 version (required)
kubeVersion: A SemVer range of compatible Kubernetes versions (optional)
description: A single-sentence description of this project (optional)
type: The type of the chart (optional)
keywords:
  - A list of keywords about this project (optional)
home: The URL of this projects home page (optional)
sources:
  - A list of URLs to source code for this project (optional)
dependencies: # A list of the chart requirements (optional)
  - name: The name of the chart (nginx)
    version: The version of the chart ("1.2.3")
    repository: (optional) The repository URL ("https://example.com/charts") or alias ("@repo-name")
    condition: (optional) A yaml path that resolves to a boolean, used for enabling/disabling charts (e.g. subchart1.enabled )
    tags: # (optional)
      - Tags can be used to group charts for enabling/disabling together
    import-values: # (optional)
      - ImportValues holds the mapping of source values to parent key to be imported. Each item can be a string or pair of child/parent sublist items.
    alias: (optional) Alias to be used for the chart. Useful when you have to add the same chart multiple times
maintainers: # (optional)
  - name: The maintainers name (required for each maintainer)
    email: The maintainers email (optional for each maintainer)
    url: A URL for the maintainer (optional for each maintainer)
icon: A URL to an SVG or PNG image to be used as an icon (optional).
appVersion: The version of the app that this contains (optional). Needn't be SemVer. Quotes recommended.
deprecated: Whether this chart is deprecated (optional, boolean)
annotations:
  example: A list of annotations keyed by name (optional).
```

Penjelasan lengkap tiap fields di dalam Chart.yaml dapat dilihat pada docs helm berikut:
[https://helm.sh/docs/topics/charts/](https://helm.sh/docs/topics/charts/)