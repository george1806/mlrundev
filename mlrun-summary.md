# MLRun CE— Script Summary

---

## 🧭 Overview of what does current script do

1. **Clones & pins** the MLRun CE chart to the latest non‑RC tag.
2. **Renders** the chart and **extracts every `image:`** reference.
3. **Pulls & saves** each image as a local `.tar` under `offline-images/` and logs a mapping in `images-list.txt`.
4. **Logs in to Harbor**, **retags**, and **pushes** all images to `HARBOR_DOMAIN/HARBOR_PROJECT` (with one retry on failures).

---

## 🔧 Step-by-Step Actions

1. **Stage 1 – Prepare locally**
    - Clone `mlrun/ce` → checkout latest `mlrun-ce-*` tag (non‑RC).
    - `helm dependency update charts/mlrun-ce`.
    - `helm template` → grep `image:` → unique list.
    - For each image: `docker pull` → `docker save` to `offline-images/` → log to `images-list.txt`.
    - Also: `docker pull postgres:16.2` → save → log.
2. **Stage 2 – Push to Harbor**
    - `docker login ${HARBOR_DOMAIN}`.
    - For each source image in `images-list.txt`:
        - Build `DST=${HARBOR_DOMAIN}/${HARBOR_PROJECT}/${name}:${tag}`.
        - `docker tag SRC DST` → `docker push DST` (retry once on failure).

---

## 📦 Image Inventory (by component)

-   **MLRun Core**
    -   `mlrun/mlrun-api:<tag>`
    -   `mlrun/mlrun-ui:<tag>`
    -   `mlrun/jupyter:<tag>`
-   **Nuclio**
    -   `quay.io/nuclio/dashboard:<tag>`
    -   `quay.io/nuclio/controller:<tag>`
-   **Object Storage (MinIO)**
    -   `minio/minio:<tag>` (or `quay.io/minio/minio:<tag>`)
    -   `minio/mc:<tag>` (or `quay.io/minio/mc:<tag>`)
-   **MPI Operator**
    -   `mpioperator/mpi-operator:<tag>`
-   **Spark Operator**
    -   `kubeflow/spark-operator:<tag>` _(`gcr.io/spark-operator/_`)\*
-   **Kubeflow Pipelines** _(chart-dependent)_
    -   `gcr.io/ml-pipeline/api-server:<tag>`
    -   `gcr.io/ml-pipeline/persistenceagent:<tag>`
    -   `gcr.io/ml-pipeline/scheduledworkflow:<tag>`
    -   `gcr.io/ml-pipeline/frontend:<tag>`
    -   `gcr.io/ml-pipeline/viewer-crd-controller:<tag>`
    -   `gcr.io/ml-pipeline/cache-server:<tag>`
    -   `gcr.io/ml-pipeline/metadata-writer:<tag>`
    -   `gcr.io/ml-pipeline/kfp-driver:<tag>`
-   **Monitoring (optional, if enabled)**
    -   `quay.io/prometheus/prometheus:<tag>`
    -   `grafana/grafana:<tag>`
    -   plus common exporters/operators (as dependents)
