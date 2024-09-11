gcloud auth login
gcloud config set project marlab10

CLUSTER_NAME=cluster001
COMPUTE_REGION=us-europe-west3
gcloud services enable container.googleapis.com
gcloud container clusters create $CLUSTER_NAME --enable-cost-allocation --region=$COMPUTE_REGION

gcloud container clusters create $CLUSTER_NAME --enable-cost-allocation --region=$COMPUTE_REGION --disk-size=100 --disk-type=pd-balanced --num-nodes=2
gcloud container clusters create example-cluster --disk-type pd-ssd
gcloud container clusters update example-cluster --enable-cost-allocation
gcloud container clusters describe example-cluster


bigquerydatatransfer.googleapis.com

Visualiza un desglose detallado de los costos de los clústeres:
https://cloud.google.com/kubernetes-engine/docs/how-to/cost-allocations?hl=es-419

1. Habilitar Kubernetes Engine API: 
    gcloud services enable container.googleapis.com
2. Creo cluster GKE:
    gcloud container clusters create example-cluster --disk-type pd-ssd --zone us-east1-c
3. Habilito cost allocation
    gcloud container update example-cluster --enable-cost-allocation
    gcloud container describe example-cluster (buscar costManagementConfig)

Set up Cloud Billing data export to BigQuery:
https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup#create-bq-dataset

4. Habilitar BigQuery Data Transfer Service API:
    gcloud services enable bigquerydatatransfer.googleapis.com
5. Crear un dataset Bigquery:
    en consola. creo all_billing_data
6. Habilito exportacion de Cloud Billing al dataset de BigQuery.
    En billing export.

7. Visualizo 

Obtén los costos después los de créditos por espacio de nombres
SELECT
  labels.value as namespace,
  SUM(cost) + SUM(IFNULL((SELECT SUM(c.amount) FROM UNNEST(credits) c), 0)) AS cost_after_credits,
FROM `project-ID.dataset.gcp_billing_export_resource_v1_XXXXXX-XXXXXX-XXXXXX`
LEFT JOIN UNNEST(labels) as labels
  ON labels.key = "k8s-namespace"
GROUP BY namespace
;

Reemplaza BILLING_DATASET_TABLE por el nombre del conjunto de datos que creaste en BigQuery.
El nombre de la tabla es similar a gcp_billing_export_resource_v1_<BILLING_ACCOUNT_ID>.