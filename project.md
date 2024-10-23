---
---
---

# **Prediction Assignment Writeup**

**Author**: Sulaiman Allouh

**Date**: 23/Oct/2024

**Load the Data**

```{Load the data}
# Load necessary libraries
library(caret)
library(lubridate)

# Load the data
training_data_url <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
testing_data_url <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"

training <- read.csv(training_data_url)
testing <- read.csv(testing_data_url)
```

**Preprocess the Data**

```{Preprocess the Data}
# Remove columns with mostly NA values
training <- training[, colSums(is.na(training)) < nrow(training) * 0.9]
testing <- testing[, colSums(is.na(testing)) < nrow(testing) * 0.9]

# Remove irrelevant columns
training <- training[, !grepl("^X|timestamp|window", names(training))]
testing <- testing[, !grepl("^X|timestamp|window", names(testing))]

# Convert the 'classe' variable to a factor
training$classe <- as.factor(training$classe)
```

**Split the Training Data**

```{Split the Training Data}
set.seed(123)
inTrain <- createDataPartition(training$classe, p = 0.7, list = FALSE)
train_set <- training[inTrain, ]
validation_set <- training[-inTrain, ]
```

**Train Models**

```{Train Models}
# Random Forest
set.seed(62433)
rf_model <- train(classe ~ ., data = train_set, method = "rf")

# Boosted Trees
gbm_model <- train(classe ~ ., data = train_set, method = "gbm", verbose = FALSE)

# Linear Discriminant Analysis
lda_model <- train(classe ~ ., data = train_set, method = "lda")
```

**Evaluate Models**

```{Evaluate Models}
# Random Forest
rf_predictions <- predict(rf_model, validation_set)
rf_accuracy <- confusionMatrix(rf_predictions, validation_set$classe)$overall['Accuracy']

# Boosted Trees
gbm_predictions <- predict(gbm_model, validation_set)
gbm_accuracy <- confusionMatrix(gbm_predictions, validation_set$classe)$overall['Accuracy']

# Linear Discriminant Analysis
lda_predictions <- predict(lda_model, validation_set)
lda_accuracy <- confusionMatrix(lda_predictions, validation_set$classe)$overall['Accuracy']

# Print accuracies
rf_accuracy
gbm_accuracy
lda_accuracy
```

**Stack Models**

```{Stack Models}
# Combine predictions into a data frame
stacked_data <- data.frame(rf = rf_predictions, gbm = gbm_predictions, lda = lda_predictions, classe = validation_set$classe)

# Fit a random forest model to the stacked predictions
stacked_model <- train(classe ~ ., data = stacked_data, method = "rf")

# Predict on the validation set using the stacked model
stacked_predictions <- predict(stacked_model, stacked_data)
stacked_accuracy <- confusionMatrix(stacked_predictions, validation_set$classe)$overall['Accuracy']

# Print stacked model accuracy
stacked_accuracy
```

**Predict on Test Data**

```{Predict on Test Data}
# Predict on the test set
test_predictions <- predict(stacked_model, testing)

# Print test predictions
test_predictions
```

**Predict on Test Data**

```{Predict on Test Data}
# Predict on the test set
test_predictions <- predict(stacked_model, testing)

# Print test predictions
test_predictions
```
