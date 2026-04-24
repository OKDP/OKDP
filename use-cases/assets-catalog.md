# OKDP — Available assets catalog

Inventory of OKDP assets available for consumption by adopters and integrators. Referenced primarily by [UC-01](use-case-01-consume-assets.md), but applicable to any use case that builds on top of OKDP components.

| Component | Available |
| :--- | :--- |
| **Apache Spark** | • [Helm chart (History Server)](https://github.com/OKDP/spark-history-server/tree/main/helm/spark-history-server)<br>• [Auth filter plugin](https://github.com/OKDP/okdp-spark-auth-filter)<br>• [Spark Web Proxy (live UI)](https://github.com/OKDP/spark-web-proxy)<br>• [Docker images & automated builds](https://github.com/OKDP/spark-images) |
| **Hive Metastore** | • [Helm chart](https://github.com/OKDP/hive-metastore/tree/main/helm/hive-metastore)<br>• [Docker images](https://github.com/OKDP/hive-metastore) |
| **Trino** | • OIDC config example |
| **Polaris Catalog** | • Trino support example<br>• OIDC config example<br>• S3/STS connectivity example |
| **Apache Superset** | • OIDC config example |
| **JupyterHub / Lab** | • Values example<br>• Docker images & JupyterLab build pipeline |

Gaps and in-progress items for each component are tracked as GitHub Issues against the corresponding use case.
