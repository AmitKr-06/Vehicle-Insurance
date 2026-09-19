# Vehicle-Insurance
End-to-end MLOps pipeline predicting customer response to vehicle insurance offers. Pulls data from MongoDB Atlas, validates and transforms it, trains a RandomForestClassifier, evaluates against production models, and pushes accepted models to AWS S3.


# Vehicle Insurance — ML Prediction Pipeline

An end-to-end MLOps pipeline that predicts whether a customer will respond positively to a vehicle insurance offer. The pipeline pulls raw data from MongoDB Atlas, validates and transforms it, trains a RandomForest classifier, evaluates it against the current production model, and automatically pushes accepted models to AWS S3.

## Tech Stack

- **Data storage:** MongoDB Atlas
- **Model storage / registry:** AWS S3
- **ML:** scikit-learn (RandomForestClassifier), imbalanced-learn (SMOTEENN)
- **Language:** Python 3.10
- **Deployment:** Docker

## Pipeline Stages

1. **Data Ingestion** — Pulls data from a MongoDB collection, exports it to CSV, and splits it into train/test sets.
2. **Data Validation** — Validates schema (column count, required numerical/categorical columns) against `config/schema.yaml`.
3. **Data Transformation** — Applies feature engineering (gender mapping, dummy variables, scaling) and handles class imbalance with SMOTEENN.
4. **Model Trainer** — Trains a RandomForestClassifier and evaluates it against an expected accuracy threshold.
5. **Model Evaluation** — Compares the newly trained model's F1 score against the current production model (if any) stored in S3.
6. **Model Pusher** — If the new model performs better, uploads it to the S3 model registry.

## Project Structure

├── src/
│ ├── components/ # Core pipeline stages (ingestion, validation, transformation, training, evaluation, pusher)
│ ├── configuration/ # MongoDB and AWS connection handling
│ ├── constants/ # Project-wide constants
│ ├── data_access/ # MongoDB data access layer
│ ├── entity/ # Config and artifact dataclasses, estimator, S3 estimator
│ ├── cloud_storage/ # AWS S3 interaction layer
│ ├── pipeline/ # Training pipeline orchestration
│ ├── utils/ # Shared utility functions
│ ├── logger.py
│ └── exception.py
├── config/
│ ├── schema.yaml # Expected dataset schema
│ └── model.yaml # Model hyperparameter config
├── app.py # Application entry point
├── Dockerfile
├── requirements.txt
└── template.py # Project scaffolding script


## Setup

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/Vehicle-Insurance.git
cd Vehicle-Insurance
```

### 2. Create a virtual environment
```bash
conda create -n vehicle python=3.10 -y
conda activate vehicle
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Set up environment variables
Create a `.env` file in the project root with:
MONGODB_URL=your_mongodb_connection_string
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key


### 5. Create the project root anchor file
```bash
New-Item -Path .project-root -ItemType File
```

## Running the Pipeline

Run each pipeline stage sequentially:
```bash
python demo.py
```

Or trigger the full training pipeline directly:
```python
from src.pipeline.training_pipeline import TrainPipeline

pipeline = TrainPipeline()
pipeline.run_pipeline()
```

## Model Performance

Current trained model metrics:
- **F1 Score:** ~0.93
- **Precision:** ~0.88
- **Recall:** ~0.99
