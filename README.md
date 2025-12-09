# Predict Future Sales Model Card
## Basic Information

- **People or organization developing model:** Fabiha Hatem [hatemfabiha@gwu.edu](mailto:hatemfabiha@gwu.edu), Abigail Liang [abigail.liang@gwu.edu](mailto:abigail.liang@gwu.edu), Bella Luz [bella.luz@gwu.edu](mailto:bella.luz@gwu.edu), Wesley Tse [wesley.tse@.gwu.edu](mailto:wesley.tse@.gwu.edu)

- **Model date:** December 2025

- **Model Version:** 1.0

- **License:** Apache 2.0 License

- **Model implementation code:** [Predict Future Sales Project Notebook](https://github.com/fabiha-hatem/Group5_Project/blob/main/DNSC3288_Project.ipynb)


## Intended Use

- **Primary intended uses:** Predicting monthly item-level sales for each (shop_id, item_id) pair while supporting inventory planning, demand forecasting, and resource allocation.

- **Primary intended users:** Students and instructors who want to explore predictive modeling techniques in an academic setting.

- **Out-of-scope use cases:** Model should not be used for real business decisions or any high-stakes forecasting outside the scope of this course.

## Training Data

- **Source of Training Data:** Predict Future Sales dataset from Kaggle’s Competitive Data Science: Predict Future Sales competition https://www.kaggle.com/c/competitive-data-science-predict-future-sales

- **How the training data was divided into training data and validation data:** The data was split temporally using the date_block_num variable (month index), ensuring no data leakage:
  
  - Training set: all rows where date_block_num < 33
  
  - Validation set: all rows where date_block_num == 33
  
  - Test set: all rows where date_block_num == 34
  
  Early months (data_blocks_num < 3) were removed to ensure all lag features were fully defined.

- **Number of rows in traing and validation data**
  
  - Training rows: 9,552,883
  
  - Validation rows: 238,172

- ### Data Dictionary

| Name | Modeling Role | Measurement Level | Description |
|------|--------------|-------------------|-------------|
| date_block_num | input | integer (ordinal) | Month index representing time progression (0 = January 2013) |
| shop_id | input | integer (categorical) | Unique identifier for each shop |
| item_id | input | integer (categorical) | Unique identifier for each item |
| item_category_id | input | integer (categorical) | Category identifier for each item |
| item_cnt_month | target | float (continuous) | Total number of items sold for a shop–item pair in a given month |
| item_cnt_month_lag_1 | input | float (continuous) | Item sales in the previous month |
| item_cnt_month_lag_2 | input | float (continuous) | Item sales two months prior |
| item_cnt_month_lag_3 | input | float (continuous) | Item sales three months prior |
| date_item_avg_cnt_lag_1 | input | float (continuous) | Average monthly item sales across all shops (lagged by one month) |
| date_shop_avg_cnt_lag_1 | input | float (continuous) | Average monthly shop sales across all items (lagged by one month) |
| item_name | metadata (excluded) | string | Text name of the item (removed before modeling) |
| item_category_name | metadata (excluded) | string | Text name of the item category (removed before modeling) |
| shop_name | metadata (excluded) | string | Text name of the shop (removed before modeling) |

## Test Data

- **Source of test data:** Kaggle “Predict Future Sales” competition dataset (test.csv).
- **Number of rows in test data:** 214,200
- **State any differences in columns between training and test data:**
The test dataset does not include the target column item_cnt_month, which is present in the training data. It also includes an ID column used only for producing the final submission file. After preprocessing, all text-based fields (item_name, item_category_name, shop_name) are removed from both the training and test datasets. Aside from the expected absence of the target and presence of ID, the test set contains the same feature columns used by the model.

## Quantitaive Analysis

  To evaluate our model, we used Root Mean Squared Error (RMSE), which is the standard metric for continuous forecasting tasks like Predict Future Sales. RMSE measures the average difference between our predictions and the actual monthly item sales. It is appropriate for this dataset because sales values vary across items and shops, and the metric penalizes larger errors more heavily.

  Our model achieved an RMSE of 0.8837 on the training data and 0.9402 on the validation data. This small gap suggests the model generalizes reasonably well without severe overfitting. An average error of under one unit per item-month is consistent with typical XGBoost performance on this dataset, especially given the large amount of sparsity (many zeros) and volatility in retail sales patterns.

  To understand how the model made its predictions, we examined feature importance. Lagged sales features—especially item_cnt_month_lag_1—were by far the strongest predictors. This makes sense for a time-series retail problem: recent sales behavior often signals short-term demand. Shop-level average sales and other lag windows also contributed meaningfully, while metadata like item categories or shop characteristics played a smaller role. Together with the RMSE metrics, these plots helped confirm that the model behaved as expected for a lag-based forecasting approach.

## Ethical Considerations

  There are several potential negative impacts to consider when applying this model. From a math and software perspective, the model’s strong dependence on lag features means it struggles with items or stores that lack historical data. This often results in systematic underprediction for new, niche, or low-volume products. The sparse nature of the dataset also encourages the model to smooth out rare but meaningful demand spikes. Additionally, because time-series pipelines are highly sensitive to date alignment, even a small mistake in constructing lag features or merging datasets could cause data leakage, inflating validation performance while leading to unreliable real-world results.

  Real-world risks primarily relate to how stores and managers might use the forecasts. Underpredicting demand can lead to stockouts and lost revenue, while overpredicting can result in excess inventory and higher costs. These effects may be felt unevenly—smaller shops or specialized categories tend to suffer more from inaccurate predictions because the model has limited information to learn from. Forecasts may also influence staffing or budgeting, meaning employees and managers could be indirectly impacted if predictions are interpreted as more precise than they actually are.

  There are also uncertainties surrounding how these impacts unfold. The model assumes that historical patterns persist, but retail environments shift constantly due to changes in customer behavior, economic trends, competitor actions, and store operations. Since the dataset does not include many of these external drivers—such as promotions, holidays, or local events—there is unavoidable uncertainty in every prediction. The XGBoost structure, while powerful, provides limited interpretability, which can make it harder for decision-makers to understand or question unexpected forecasts.

Finally, several unexpected results emerged during model evaluation. Lag-1 sales dominated feature importance, overshadowing item metadata and shop characteristics, which suggests the model leaned heavily on simple momentum effects. Items with no prior sales occasionally received small positive predictions due to smoothing behavior. Shop-level average sales also played a larger role than
