# Transformer-Based-approach-for-hand-gesture-recognition-through-EMG-signals
The project aims at designing and implementing a transformer deep neural network model for classifying hand gestures using electromyogram signals. The model should distinguish several hand gestures where a public EMG dataset will be employed for training and evaluating the proposed model.

# Project Overview

This repository implements a transformer-based approach for data processing and model training. The project is structured to handle multiple datasets (DB2, DB3, DB4), with dedicated directories for data storage, model training, and results visualization.

# Repository Structure

├── data
│   ├── DB2
│   │   ├── dataset
│   │   ├── normalized_data
│   │   ├── preprocessed_data
│   ├── DB3
│   ├── DB4
│
├── models
│   ├── DB2
│   ├── DB3
│   ├── DB4
│
├── results
│   ├── DB2
│   │   ├── DB2_confusion_matrix
│   │   ├── DB2_graphs
│   │   ├── DB2_xlsx
│   ├── DB3
│   ├── DB4
│
├── .gitignore
├── create_dataset_vf.ipynb
├── README.md
├── transformer_mode_training.ipynb

# Description of Key Components

- data/: Contains datasets for DB2, DB3, and DB4, along with their preprocessed and normalized versions.

- models/: Stores trained models for different datasets.

- results/: Includes evaluation metrics like confusion matrices, graphs, and exported data files for each dataset.

- create_dataset_vf.ipynb: Jupyter notebook for dataset creation and preprocessing.

- transformer_mode_training.ipynb: Notebook for training transformer-based models.

- .gitignore: Specifies files to be ignored in version control.


# Installation & Usage

1- Clone the repository:
git clone https://github.com/your-repo.git
cd your-repo

2- Install dependencies:
pip install -r requirements.txt

3- Run the dataset creation script:
jupyter notebook create_dataset_vf.ipynb

4- Train the model:
jupyter notebook transformer_mode_training.ipynb

# Contributions
Feel free to contribute by submitting issues or pull requests.

# license 