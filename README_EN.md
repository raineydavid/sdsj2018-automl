Sberbank Data Science Journey 2018: AutoML
==========================================
Materials for the competition in automated machine learning SDSJ 2018 AutoML.

## Task Description
SDSJ AutoML is a competition in the construction of machine learning algorithms that automatically perform actions such as data preprocessing, selection of a family of models, selection of hyperparameters.

## Dataset format
The data is transmitted to the solution in CSV format. The data columns contain the following columns:

- `line_id` - line id
- `target` is the target variable (for the training set only), the real number for the regression and 0/1 for the classification
- `<type> _ <feature>` - a sign of one of the possible types (type):
- `number` is a number format (may contain a quantitative, categorical, or binary value)
- `string` - string format
- `datetime` - the date in the format 2010-01-01 or the date / time in the format 2010-01-01 10:10:10
- `id` - id (categorical value of a special nature)

## Solution format

It is necessary to send the algorithm code packaged in a ZIP archive to the testing system. Solutions run in isolated environments using Docker, time and resources for testing are limited. In the simple case, the participant does not need to deal with the Docker technology.

In the root of the archive there must be a file metadata.json with the following content:
```json
{
    "image": "sberbank / python",
    "entry_points": {
        "train_classification": "python train.py --mode classification --train-csv {train_csv} --model-dir {model_dir}",
        "train_regression": "python train.py --mode regression --train-csv {train_csv} --model-dir {model_dir}",
        "predict": "python predict.py --test-csv {test_csv} --prediction-csv {prediction_csv} --model-dir {model_dir}"
    }
}
```
Here, image is the field with the name of the docker image in which the solution will be launched, entry_points are the commands with which the solution is launched (train_ * - model training in the classification and regression mode, predict - building predictions using the trained model). To solve the current directory will be the root of the archive.

In commands, it is necessary to use templates in which the necessary values will be substituted into the testing system at startup:

- `{train_csv}`, `{test_csv}` - the path to the CSV file with a training or test sample
- `{model_dir}` - the path to the previously created directory in which you need to put a trained model for later use
- `{prediction_csv}` - path to the file to which the predictions should be written

When a solution is launched, the `TIME_LIMIT` environment variable specifies the maximum time (in seconds) that the algorithm is allowed to run. It is guaranteed that the algorithm is given a separate training and prediction time of at least 300 seconds, however, this limit is raised for large data sets.

You can use existing environments to run solutions:

`sberbank / python` - Python3 with a large set of libraries installed (more)
`gcc` - to run compiled C / C ++ solutions (read more here)
`node` - to run javascript
`openjdk` - for java
`mono` - for C #
Any other image available for download from DockerHub will do. If necessary, you can prepare your image, add the necessary software and libraries to it (see the instructions for creating Docker images); to use it you will need to publish on DockerHub.

## Restrictions
The solution container is launched under the following conditions:

- resources available to the solution
  - 12 GB of RAM
  - 4 vCPU
- the solution does not have access to Internet resources
- maximum size of a packed and unpacked archive with a solution: 1 GB
- The archive is unpacked into a file system in RAM (ramfs) available to the write solution.
- the rest of the container is read only.
- CSV with a set of data does not exceed 3 GB

## Rating system
1. For each task (dataset) for the test part of the sample, a task-specific metric is considered (RMSE for regression, ROC-AUC for binary classification).
2. For each task (dataset), the value of the participants' metrics is translated into a common scale according to the following scheme. For the best metric solution (among all sent and successfully tested solutions) 1 point is given, the baseline solution is estimated at 0 points. Participants who are on the metric between the best and baseline solutions receive a proportional score between 0 and 1. Decisions on the quality of the bottom baseline are estimated at 0 points. If the best solution and the baseline solution coincide, then all participants receive 0 points. If the participant's decision gives an error on the task or does not pass through the time limit, then it receives 0 points for this task.
3. The final result of each participant is counted as the sum of the results for each task after the transfer to the general scale. In general leaderboard participants are ranked by the final result.

## Startup examples and data set examples
Public dataset for local testing solutions: [sdsj2018_automl_check_datasets.zip](https://s3.eu-central-1.amazonaws.com/sdsj2018-automl/public/sdsj2018_automl_check_datasets.zip)
