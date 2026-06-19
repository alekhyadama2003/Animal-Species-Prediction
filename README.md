<div align="center">

<img src="https://img.shields.io/badge/-Animal%20Species%20Prediction-8c1d18?style=for-the-badge&labelColor=5c1310" height="46"/>

### Final Year Capstone Project
### Lovely Professional University

<br>

<table>
<tr><td align="center">

| | |
|---|---|
| **Project Title** | Animal Species Prediction using Deep Learning |
| **Mentor** | Jobanpreet Singh |
| **Team Member** | Alekhya Dama |
| **Role** | Team Leader |
| **Project Role** | Programmer (Python, SQL, DBMS) |
| **Domain** | Artificial Intelligence / Deep Learning / Data Engineering |

</td></tr>
</table>

<br>

![Python](https://img.shields.io/badge/Python-3.12-c79a2e?style=flat-square&logo=python&logoColor=white&labelColor=5c1310)
![TensorFlow](https://img.shields.io/badge/TensorFlow-Keras-c79a2e?style=flat-square&logo=tensorflow&logoColor=white&labelColor=5c1310)
![SQLite](https://img.shields.io/badge/Database-SQLite3-c79a2e?style=flat-square&logo=sqlite&logoColor=white&labelColor=5c1310)
![Flask](https://img.shields.io/badge/Web-Flask-c79a2e?style=flat-square&logo=flask&logoColor=white&labelColor=5c1310)
![License](https://img.shields.io/badge/License-MIT-c79a2e?style=flat-square&labelColor=5c1310)

</div>

---

## 📑 Table of Contents

1. [Abstract](#-abstract)
2. [Introduction & Problem Statement](#-introduction--problem-statement)
3. [Objectives](#-objectives)
4. [Literature Review](#-literature-review)
5. [Technology Stack](#-technology-stack)
6. [System Architecture](#-system-architecture)
7. [Database Design (DBMS)](#-database-design-dbms)
8. [Dataset](#-dataset)
9. [Methodology](#-methodology)
10. [Project Structure](#-project-structure)
11. [Installation & How to Run](#-installation--how-to-run)
12. [Results & Output Screenshots](#-results--output-screenshots)
13. [Model Performance](#-model-performance)
14. [Team & Roles](#-team--roles)
15. [Future Scope](#-future-scope)
16. [References](#-references)
17. [Acknowledgement](#-acknowledgement)
18. [License](#-license)

---

## 📘 Abstract

This capstone project presents an **Animal Species Prediction System** that classifies images of animals into their respective species using a **Convolutional Neural Network (CNN)** built with TensorFlow/Keras. Beyond the deep-learning model, the project implements a complete, production-style pipeline: an image preprocessing module, a normalised **SQL (SQLite) database** that stores species metadata, dataset statistics, model versions and a full audit log of every prediction made, and a lightweight **Flask web application** that lets a user upload an image and instantly see the predicted species, confidence score, and historical accuracy statistics queried live from the database. The project demonstrates the integration of three core disciplines required of the capstone: **Artificial Intelligence / Deep Learning**, **Python application engineering**, and **SQL / DBMS design** — fulfilling the "Programmer (Python, SQL, DBMS)" role on the team.

## 🧩 Introduction & Problem Statement

Manual identification of animal species from images is time-consuming and requires domain expertise — a bottleneck for wildlife researchers, conservation NGOs, zoological parks, and educational platforms that need to process large volumes of camera-trap or crowd-sourced images. This project addresses that problem by building an automated image-classification pipeline that:

- Accepts an animal image as input,
- Predicts the most likely species using a trained CNN, and
- Persists every prediction, along with confidence scores and ground-truth labels (when available), into a relational database for analytics, auditing, and continuous model evaluation.

## 🎯 Objectives

- Design and train a CNN-based image classifier capable of distinguishing between multiple animal species.
- Build a clean, reusable **data preprocessing pipeline** (resizing, normalisation, augmentation).
- Design a **normalised relational database schema (3NF)** to store species metadata, dataset composition, model versions, and a full prediction audit log.
- Integrate the trained model with the database through a Python data-access layer (`database.py`).
- Build a simple **Flask web interface** for end-users to upload images and view predictions and live accuracy dashboards.
- Evaluate the model using standard metrics: accuracy, precision, recall, F1-score, and a confusion matrix.
- Document the system with architecture and ER diagrams for academic submission.

## 📚 Literature Review

Image classification has been revolutionised by **Convolutional Neural Networks (CNNs)**, which automatically learn hierarchical spatial features (edges → textures → shapes → objects) directly from pixel data, removing the need for hand-crafted feature engineering used in classical machine learning. Landmark architectures such as **LeNet**, **AlexNet**, **VGGNet**, and more recent lightweight networks like **MobileNetV2** have shown that deep stacked convolution + pooling layers, combined with regularisation techniques like **Dropout** and **Batch Normalisation**, generalise well to image-recognition tasks. For domains with limited labelled data, **transfer learning** — reusing the convolutional base of a network pretrained on a large corpus such as ImageNet — is a widely adopted technique to boost accuracy while reducing training time, and is included in this project (`model.build_transfer_learning_model`) as the recommended production path. On the data-engineering side, relational database design following **normalisation principles (1NF–3NF)** ensures prediction logs and dataset metadata remain consistent, query-efficient, and free of redundancy — a standard requirement in any applied ML system that needs auditability.

## 🛠 Technology Stack

| Layer | Technology |
|---|---|
| Programming Language | Python 3.12 |
| Deep Learning Framework | TensorFlow / Keras (CNN, optional MobileNetV2 transfer learning) |
| Data Handling | NumPy, Pillow (PIL) |
| Visualization | Matplotlib, Seaborn |
| Evaluation | scikit-learn (`classification_report`, `confusion_matrix`) |
| Database | SQLite3 (portable to MySQL / PostgreSQL) |
| Backend / Web App | Flask |
| Frontend | HTML5, CSS3 (Jinja2 templates) |
| Version Control | Git & GitHub |

## 🏗 System Architecture

<p align="center">
  <img src="outputs/diagrams/system_architecture.png" alt="System Architecture Diagram" width="850">
</p>

The pipeline flows in two connected loops:

1. **Training loop** — raw images are preprocessed → fed into the CNN → the trained model is serialized to `saved_model/animal_species_cnn.keras`.
2. **Inference loop** — a user-uploaded image is preprocessed the same way → passed through the trained model via `predict.py` → the prediction (species + confidence) is returned to the user **and** written into the SQLite database, which powers the live dashboard (`/history`, per-species accuracy) in the Flask app.

## 🗄 Database Design (DBMS)

The relational schema (`database/schema.sql`) is normalised to **3NF** and consists of five tables and two analytical views:

<p align="center">
  <img src="outputs/diagrams/er_diagram.png" alt="Entity Relationship Diagram" width="850">
</p>

| Table | Purpose |
|---|---|
| `Species` | Master list of species the model can predict (name, scientific name, habitat, conservation status) |
| `Dataset_Info` | Train/test image counts and source per species |
| `Model_Version` | Metadata for each trained model artifact (architecture, accuracy, epoch count) — supports reproducibility |
| `Prediction_Log` | Every inference made by the system: image path, predicted/actual species, confidence, **computed `is_correct` column**, timestamp |
| `Users` | Basic role-based users for the web app (`Admin`, `Researcher`, `Guest`) |
| `Species_Accuracy` (view) | Live per-species accuracy computed from `Prediction_Log` |
| `Recent_Predictions` (view) | Human-readable join of the latest predictions with species names |

All foreign keys are enforced (`PRAGMA foreign_keys = ON`), and indexes are added on `Prediction_Log(predicted_species_id)` and `Prediction_Log(prediction_timestamp)` for fast analytical queries.

## 🐾 Dataset

For full **offline reproducibility** inside any environment (including this submission's execution sandbox, which has no internet access to download external datasets), this repository ships with `src/generate_synthetic_dataset.py`, which procedurally renders a small labelled image dataset (6 classes — Cat, Dog, Elephant, Horse, Lion, Tiger — 220 train / 45 test images per class, 64×64 px) with class-distinctive shape, colour and texture signatures plus random jitter and noise. This lets the **entire pipeline — training, evaluation, plotting, and SQL logging — run end-to-end out of the box** and produce the real results shown below.

> **For production / real submission use:** replace the synthetic generator with an actual photographic dataset such as the public [Animals-10 dataset (Kaggle)](https://www.kaggle.com/datasets/alessiocorrado99/animals10), keeping the same `data/train/<species>/*.jpg` and `data/test/<species>/*.jpg` folder structure. No other code changes are required — `train.py` will pick it up automatically. For photographic data, switch `model.py` to `build_transfer_learning_model()` (MobileNetV2 backbone) for significantly better accuracy than a from-scratch CNN.

## ⚙ Methodology

1. **Data Acquisition** — synthetic demo dataset generated (or real dataset placed) under `data/train` and `data/test`.
2. **Preprocessing** (`data_preprocessing.py`) — images resized to 64×64, pixel values normalised to [0, 1], labels integer-encoded, training data shuffled.
3. **Model Building** (`model.py`) — a CNN with 3 convolution + max-pooling blocks, on-the-fly data augmentation (flip, rotation, zoom, translation), `GlobalAveragePooling2D`, and a dense classification head with Dropout regularisation.
4. **Training** (`train.py`) — trained with the Adam optimizer, sparse categorical cross-entropy loss, and `EarlyStopping` on validation loss.
5. **Evaluation** — accuracy/loss curves, confusion matrix, classification report (precision/recall/F1), and a sample-prediction visual grid are generated automatically.
6. **Persistence** — trained model saved as `.keras`; species metadata, dataset stats, model version, and every test-set prediction are written to the SQLite database.
7. **Serving** (`app.py`) — Flask web app exposes an upload form, a results page, a prediction-history page (SQL-backed), and JSON API endpoints.

## 📂 Project Structure

```
Animal-Species-Prediction/
├── data/                          # Generated/placed image dataset (train/test, per-class folders)
├── database/
│   ├── schema.sql                 # SQL DDL — tables, foreign keys, indexes, views
│   └── animal_species.db          # SQLite database (generated on first run)
├── saved_model/
│   ├── animal_species_cnn.keras   # Trained CNN model
│   └── class_names.json
├── outputs/
│   ├── training_history.png       # Accuracy / loss curves
│   ├── confusion_matrix.png       # Test-set confusion matrix
│   ├── sample_predictions.png     # Sample predictions grid
│   ├── classification_report.txt
│   └── diagrams/
│       ├── system_architecture.png
│       └── er_diagram.png
├── src/
│   ├── generate_synthetic_dataset.py
│   ├── data_preprocessing.py
│   ├── model.py                   # CNN + transfer-learning architectures
│   ├── train.py                   # Full training + evaluation + DB logging pipeline
│   ├── predict.py                 # Single-image inference CLI
│   ├── database.py                # SQL data-access layer
│   ├── app.py                     # Flask web application
│   └── make_diagrams.py           # Generates architecture/ER diagrams
├── templates/                     # Flask Jinja2 HTML templates
│   ├── base.html
│   ├── index.html
│   ├── result.html
│   └── history.html
├── static/uploads/                # User-uploaded images (web app)
├── requirements.txt
└── README.md
```

## 🚀 Installation & How to Run

```bash
# 1. Clone the repository
git clone https://github.com/alekhyadama2003/Animal-Species-Prediction.git
cd Animal-Species-Prediction

# 2. Install dependencies
pip install -r requirements.txt

# 3. (Optional) Generate the demo dataset explicitly
python3 src/generate_synthetic_dataset.py

# 4. Train the model — runs preprocessing, training, evaluation,
#    generates all output charts, and populates the SQL database
python3 src/train.py

# 5. Predict a single image from the command line
python3 src/predict.py data/test/Tiger/tiger_test_0000.png

# 6. Launch the web application
python3 src/app.py
# then open http://localhost:5000 in your browser
```

## 📊 Results & Output Screenshots

All images below were generated by **actually running** `train.py` in this repository — not mock-ups.

### Training Accuracy & Loss Curves
<p align="center">
  <img src="outputs/training_history.png" width="850">
</p>

### Confusion Matrix (Test Set)
<p align="center">
  <img src="outputs/confusion_matrix.png" width="600">
</p>

### Sample Predictions
<p align="center">
  <img src="outputs/sample_predictions.png" width="850">
</p>

*(Test accuracy is near-perfect here because the bundled demo dataset uses deterministic, clearly-separable synthetic shapes per class — see [Dataset](#-dataset). On real photographic data, expect more realistic accuracy in the 80–95% range depending on dataset size/quality and whether transfer learning is used.)*

## 📈 Model Performance

| Metric | Value (demo run) |
|---|---|
| Architecture | 3× Conv2D + GlobalAveragePooling2D + Dense |
| Epochs run (Early Stopping, patience=8) | 30 |
| Test Accuracy | 100.0% |
| Test Loss | 0.0058 |
| Per-species Precision / Recall / F1 | 1.000 / 1.000 / 1.000 (all 6 classes) |
| Per-species accuracy | queryable live via the `Species_Accuracy` SQL view / Flask dashboard |

Full precision/recall/F1 breakdown is written to `outputs/classification_report.txt` on every training run.

## 👥 Team & Roles

| Name | Role | Responsibilities |
|---|---|---|
| **Alekhya Dama** | **Team Leader** / Programmer (Python, SQL, DBMS) | CNN model design & training, database schema design, Python pipeline (preprocessing, training, inference, Flask app), SQL integration, documentation |

**Mentor:** Jobanpreet Singh, Lovely Professional University

## 🔭 Future Scope

- Replace the synthetic demo dataset with a large-scale real photographic dataset (e.g., Animals-10, iNaturalist) and switch to the bundled MobileNetV2 transfer-learning model for production-grade accuracy.
- Migrate the database layer from SQLite to MySQL/PostgreSQL for multi-user, concurrent production deployment.
- Add user authentication and role-based access control using the existing `Users` table.
- Deploy the Flask app to a cloud platform (Render/AWS/Azure) with a CI/CD pipeline.
- Extend the model to a multi-label setting (multiple animals per image) using object detection (YOLO/Faster R-CNN).
- Build an analytics dashboard (e.g., with Chart.js) on top of the existing SQL views for richer accuracy/confidence trend visualisations.

## 📖 References

1. LeCun, Y., Bengio, Y., & Hinton, G. (2015). *Deep Learning*. Nature.
2. Simonyan, K., & Zisserman, A. (2014). *Very Deep Convolutional Networks for Large-Scale Image Recognition (VGG)*.
3. Sandler, M., et al. (2018). *MobileNetV2: Inverted Residuals and Linear Bottlenecks*.
4. TensorFlow / Keras Documentation — https://www.tensorflow.org
5. SQLite Documentation — https://www.sqlite.org/docs.html
6. Flask Documentation — https://flask.palletsprojects.com
7. Animals-10 Dataset, Kaggle — https://www.kaggle.com/datasets/alessiocorrado99/animals10

## 🙏 Acknowledgement

I would like to express my sincere gratitude to my mentor, **Jobanpreet Singh**, for his valuable guidance and continuous support throughout this capstone project. I also thank the faculty of **Lovely Professional University** for providing the opportunity and resources to undertake this project as part of the final-year curriculum.

## 📄 License

This project is released under the [MIT License](LICENSE).

---

<div align="center">

Made with 🧠 + 🐍 + 🗄 for the Capstone Project @ Lovely Professional University

</div>
