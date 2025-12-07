# Predict Future Sales Model Card
## Basic Information

- **People or organization developing model:** Fabiha Hatem [hatemfabiha@gwu.edu](mailto:hatemfabiha@gwu.edu), Abigail Liang [abigail.liang@gwu.edu](mailto:abigail.liang@gwu.edu), Bella Luz [bella.luz@gwu.edu](mailto:bella.luz@gwu.edu), Wesley Tse [wesley.tse@.gwu.edu](mailto:wesley.tse@.gwu.edu)

- **Model date:** December 2025

- **Model Version:** 1.0

- **License:** Apache 2.0 License

- **Model implementation code:** [Predict Future Sales Project Notebook](https://github.com/fabiha-hatem/Group5_Project/blob/main/updatedproject5.ipynb)


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

