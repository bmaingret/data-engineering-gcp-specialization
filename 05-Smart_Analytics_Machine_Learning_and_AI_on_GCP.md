# Smart Analytics, Machine Learning, and AI on GCP

- [Productionizing Custom ML Models](#productionizing-custom-ml-models)
- [Custom Model building with SQL in BigQuery ML](#custom-model-building-with-sql-in-bigquery-ml)
- [Custom Model Building with Cloud AutoML](#custom-model-building-with-cloud-automl)

![](/assets/05-Smart_Analytics_Machine_Learning_and_AI_on_GCP/05-Smart_Analytics_Machine_Learning_and_AI_on_GCP_ml_gcp.png)

## Productionizing Custom ML Models

![](/assets/05-Smart_Analytics_Machine_Learning_and_AI_on_GCP/05-Smart_Analytics_Machine_Learning_and_AI_on_GCP_ml_project.png)

Building custom ML:

* Cloud AI Platform -> support training models and serving predictions (TF, Keras, XGBoost, sklearn), serverless
* AutoML
* BigQuery ML
  
**Kubeflow**: packaging ML models in deployable artefact to Kubernetes

> Perception: ML is a lot about model -> reality is it requires a lot of DevOps

* Kubeflow pipeline orchestrate ML workflows and ease sharing and reproducibility.
* Available Python SDK to author pipelines.
* Can be packaged 

**AI Hub**

> Repository for AI assets 

* Kubeflow pipelines
* Jupyter notebooks
* Trained Models
* VM images
* etc.

Public and **Private** assets

## Custom Model building with SQL in BigQuery ML

Model options:

* Logistic regression
* DNN classifier
* XGBoost classifier
* Linear regression
* DNN regression
* XGBoost regression
* TF (for predictions only, must be trained before)
* Recommendation engine (Matrix Factorization)
* Clustering (kmeans)

Data transformation can be included into the model 

## Custom Model Building with Cloud AutoML

* ML Vision (classification)
* ML NLP (classification)
* ML Table 

Still requires ML and coding skills (different than pretrained model API).

Data must be prepared and will be automatically analyzed to make sure there is enough data and organized enough.

**Deploy** As soon as a model is trained, it is available to serve predictions

![](/assets/05-Smart_Analytics_Machine_Learning_and_AI_on_GCP/05-Smart_Analytics_Machine_Learning_and_AI_on_GCP_matrix_choice.png)
