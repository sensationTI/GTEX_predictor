# CNN-ENCODER-XGBOOST Genotype-Tissue Expression Profile Simulator

This notebook provides the source code for the CEX pipeline, a genotype-tissue expression profile simulator.
It consists of the following module.

1. Vicinity Set Cover Algorithm for Solving Reference Genes in the Human Genome
2. Build the CNN Module
3. Build the Encoder Module
4. Build the XGBoost Module

## What it needs

We provide the following conda env that installs all necessary packages. To re-create this env on another machine, do:

```conda
conda env create -f environment.yml
```

Then, register the kernel:
```conda
conda activate myenv
python -m ipykernel install --user --name myenv --display-name "Python (myenv)"
```
Since this code runs on a NVIDIA RTX4090 (cuda12.1, cuDNN = 8.9, Tensorflow version = 2.14.0).\
If there is a different GPU, make sure you have the compatible versions, which can be found here:\
https://docs.nvidia.com/deeplearning/cudnn/backend/latest/reference/support-matrix.html


## 🔧 How It Works
Execute all cells in order.

To train the model from scratch:
```python
train_nn()
```
- Loads protein-coding gene data from a `protein2_promoter.pkl` file.
- Uses the given threshold (in base pairs) to determine vicinities.
- Saves the trained model in `cnn_encoder.keras`.

To load the trained model:
```python
best_model = keras.models.load_model("cnn_encoder.keras")
# Verify it's working
loaded_model.summary()
```

- To visualize the generated profile:
```python
show_random_profile_prediction(gene_idx)
```
- Randomly choose a gene from the testing dataset
- Visualize the predicted, reference, and experimental profiles.



## ▶️ Example Usage

```python
# train the model
best_model = train_nn()
```
This will output:
- `best_model`: The trained model.
- `lower_model`: The trained quantile regression model for predicting upper bound.
- `upper_model`: The trained quantile regression model for predicting lower bound.
- `mean_errors`: Mean estimation errors at the current threshold(s).
Additionally, to get the training and testing dataset and other useful data, add the following to the arguments:
- `X_train_features`: Training data.
- `y_train_proc`: Training label (54 expression value).
- `y_pred_median_real`: Predicted values from the median model.
- `y_pred_lower_real/y_pred_upper_real`: Predicted values from the quantile regression models.
- `testing_df`: Entire testing dataset used.
- `training_df`: Entire training dataset used.
  
```
# select a random testing sample and visualize it
idx = np.random.randint(0, len(testing_df))
show_random_profile_prediction(idx)
```
This will output:\
Profile comparisons for the predicted, experimental, and reference gene's profile.

```python
# intepret the training result
get_activation_model(cnn_model)
```
This will select the layers to be visualize in the CNN. Then:
```python
# convert top activating sequence to position frequency matrix
build_pfm(seqs):
```
Normalize to get PWM:
```python
plot_pwm(pwm, filter_index):
```


## 📁 Requirements
- Compatible versions.
- A file named `protein2_promoter.pkl` containing gene data in a dictionary format.
- Run the cell that imports the necessary libraries.
- Run the preceeding cells that define the required functions.

## 💡 Notes

- Adjust `thres` to control the size of the training sets.
- Make sure all necessary imports (e.g., `pickle`, `numpy`) are included.
