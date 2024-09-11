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

How to use GKE cost allocation data for detailed insight into cloud spend:
https://www.doit.com/how-to-use-gke-cost-allocation-data/

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



https://www.kubecost.com/

https://www.opencost.io/docs/

https://console.cast.ai/



# Cast AI

1. Conectar cluster GKE con Cast 
    desde consola con acceso kubectl:
curl -H "Authorization: Token 5fead151525b20d20c7b42f9eb3e6dc0dbbc8c86bdd09d0b231f6e3a5bf4eca9" "https://api.cast.ai/v1/agent.yaml?provider=gke" | kubectl apply -f -
2. Enable Cast AI (automation kubernetes & cloud security):
   con Helm instaldo:
CASTAI_API_TOKEN=a397fcc5842a810b3956b34e93c241fd712abd6a09e8f5af2e477d76afb72f86 CASTAI_CLUSTER_ID=4d2cdfb1-ce86-4c73-b1b6-76e8122dfe67 CLUSTER_NAME=example-cluster INSTALL_AUTOSCALER=true INSTALL_POD_PINNER=true INSTALL_SECURITY_AGENT=true LOCATION=us-east1-c PROJECT_ID=marlab10 /bin/bash -c "$(curl -fsSL 'https://api.cast.ai/v1/scripts/gke/onboarding.sh')"




instalado en cluster de willylab.sit
administrador@willylab.site via usuario Google
.Martian_2001
https://console.cast.ai/dashboard?org=ddd79eda-f095-4128-b51d-00833029ad02