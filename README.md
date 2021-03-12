# Fury Distribution Minimal

A minimimal deployment of the [Fury Kubernetes Distribution](https://github.com/sighupio/fury-distribution)

## Tools required

- kustomize `>= 3.6.1`
- furyctl `>= 0.4.0`

## Modules

The following modules of the distribution are deployed:

| Module                                                                    | Version                                  |
| -------------                                                             | -------------                            |
| [Fury Monitoring](https://github.com/sighupio/fury-kubernetes-monitoring) | d4ac34771b33611490d8c64cb458cabe874c2956 |
| [Fury Logging](https://github.com/sighupio/fury-kubernetes-logging)       | v1.7.1                                   |
| [Fury Ingress](https://github.com/sighupio/fury-kubernetes-ingress)       | d34f8228dc8b108526f8dbfbcf598c74f842832e |

You change the version by modifying the `Furyfile.yml` and then update the vendor folder via `furyctl vendor -H`
