# Flight Price Prediction

A Jupyter Notebook project for exploring flight-fare data, preparing it for machine learning, comparing regression models, and tuning an XGBoost model to predict flight prices.

## Project contents

| File | Description |
| --- | --- |
| `flight_price_prediction.ipynb` | End-to-end exploratory analysis, preprocessing, model comparison, tuning, and a sample prediction. |
| `Flight_Fare.xlsx` | Excel dataset read by the notebook. |

The notebook predicts the `Price` column from journey, airline, route-stop, and airport information. Prices in the dataset are expressed in Indian rupees (₹).

## Requirements

Use Python with Jupyter Notebook or JupyterLab. The notebook imports:

- pandas
- NumPy
- Matplotlib
- Seaborn
- scikit-learn
- XGBoost
- openpyxl (the Excel reader used by pandas for `.xlsx` files)

Install the packages in a virtual environment. For example, in Windows PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install pandas numpy matplotlib seaborn scikit-learn xgboost openpyxl jupyter
```

If PowerShell does not allow environment activation, use the Python executable directly instead:

```powershell
.\.venv\Scripts\python.exe -m pip install pandas numpy matplotlib seaborn scikit-learn xgboost openpyxl jupyter
```

## Run the notebook

1. Clone or download this repository and make sure `Flight_Fare.xlsx` is available.
2. Install the requirements above.
3. Start Jupyter from the repository folder:

   ```powershell
   jupyter notebook
   ```

4. Open `flight_price_prediction.ipynb` and run the cells from top to bottom.

### Dataset path

The notebook currently loads the workbook using the Google Colab path `/content/Flight_Fare.xlsx`. In Google Colab, upload the workbook to that path before running the data-loading cell.

To run locally from the repository folder, change that cell to:

```python
df = pd.read_excel("Flight_Fare.xlsx")
```

The first markdown cell says the workbook can be placed beside the notebook, but the current code uses the Colab-specific path; update the path as shown when running locally.

## Dataset overview

The workbook contains 10,683 rows and 11 columns in the notebook's recorded inspection. The input columns are:

| Column | Meaning |
| --- | --- |
| `Airline` | Airline name |
| `Date_of_Journey` | Journey date |
| `Source` | Departure city |
| `Destination` | Arrival city |
| `Route` | Airport route |
| `Dep_Time` | Departure time |
| `Arrival_Time` | Arrival time |
| `Duration` | Flight duration |
| `Total_Stops` | Number of stops |
| `Additional_Info` | Additional fare/flight information |
| `Price` | Fare to predict (target) |

The recorded inspection shows one missing `Route` value and one missing `Total_Stops` value; the target `Price` has no missing values.

## Notebook workflow

1. **Load and inspect** — read the Excel workbook and display sample records, missing-value counts, data types, summary statistics, columns, and dataset size.
2. **Explore** — plot numeric histograms and categorical counts, then compare prices across airline, source, and stop-count groups.
3. **Prepare features**:
   - Remove `Route` and `Additional_Info`.
   - Keep records whose `Total_Stops` is `non-stop`, `1 stop`, `2 stops`, or `3 stops`.
   - Extract journey day and month, departure and arrival hours and minutes, and duration hours and minutes.
   - Convert stop categories to numeric values from 0 to 3.
   - One-hot encode `Airline`, `Source`, and `Destination` with the first category dropped.
4. **Split and scale** — separate `Price` from the features, use an 80/20 train/test split with `random_state=42`, and fit `StandardScaler` on the training set's numeric time/date/stop features. Apply that fitted scaler to both sets.
5. **Compare regressors** — train Linear Regression, K-Nearest Neighbors, Decision Tree, Random Forest, Gradient Boosting, Support Vector Regression, and XGBoost. Compare training and test R², mean absolute error (MAE), and root mean squared error (RMSE).
6. **Tune XGBoost** — use `RandomizedSearchCV` to try 20 parameter combinations with 3-fold cross-validation, optimizing R².
7. **Make a sample prediction** — predict the fare for the first row of the test set and print its actual fare alongside the prediction.

## Evaluation metrics

- **R²** measures how much of the variation in fares the model explains; higher is generally better.
- **MAE** is the average absolute prediction error in the fare's units; lower is better.
- **RMSE** penalizes larger errors more heavily than MAE; lower is better.
- Comparing training and test R² can help identify overfitting when a model scores much better on training data than on unseen test data.

## Results and reproducibility

The saved output from one model-comparison run ranks XGBoost highest, with a test R² of 0.867, MAE of ₹1,112.5, and RMSE of ₹1,671.0. The saved tuned-model output reports a test R² of 0.8675 and MAE of ₹1,121.6. These are recorded notebook outputs, not a guarantee of future performance.

**Important:** the notebook also contains a later, manually written report with different comparison and tuning metrics. That report says duplicate rows were removed, but the executable preprocessing code does not call `drop_duplicates()`. Treat the code you run—not those conflicting saved outputs or narrative figures—as the source of truth. Rerun the notebook to obtain results for your environment and dataset, and update the report if you revise the workflow.

## Limitations

- The sample prediction is for an existing test-set row; the notebook does not provide a form or function for entering an arbitrary new flight.
- The notebook does not save or export the trained model and does not include a web service or application.
- `Route` and `Additional_Info` are discarded, so route-level details and other information in those fields are not used directly.
- The recorded dataset is from 2019. Fare patterns can change over time; evaluate the model on current, representative data before relying on its predictions.
- A single train/test split and the metrics in a notebook run do not establish performance for every airline, route, or future booking.

## License and data

No license or separate data-source attribution is specified in the repository. Check the dataset's terms and add the appropriate attribution or license information before redistributing the workbook or project.
