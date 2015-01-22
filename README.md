Course Project for Getting and Cleaning Data Course
================================================

## Introduction

For this project, the dataset I worked with is the ***Human Activity Recognition Using Smartphones Data Set  from the given*** from the UCI Machine Learning Repository. The data is collected from the recordings of **30** subjects performing **6** different types daily living activities while carrying a waist-mounted smartphone with embedded inertial sensors: 

- walking

- walking upstairs

- walking downstairs

- sitting

- standing

- laying

The full description can be found [here](http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones) and the dataset can be manually downloaded [here](https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip). However, the script automatically downloads the data directory so there's no need to manually download it. 

Included in this repository are **4** files:

- `README.md` - this file providing the overview of the project and files included in this repository

- `Codebook.md` - describes all of the variables

- `run_analysis.R` - script that downloads the provided data, processed it, and outputs the clean dataset

- `UCI HAR Tidy Averages Dataset.txt` - final tidy dataset with the average values of each variable for each activity and each subject

## Original Data

The original data repository included **4** text files and **2** folders:

- `activity_labels.txt` - the mapping of numeric values 1 to 6 to the 6 activities previously described

- `features.txt` - list of all variable names for 561 variables

- `features_info.txt` - explaination for how the 561 variables are created and what they mean

- `README.txt` - overview of dataset

- **`test`** - folder containing the *test* subset of data (2947 observations), which contain 3 text files and 1 folder

    - `Inertial Signal` - raw signal data from the sensors, irrelevant for this project
    
    - `subject_test.txt` - mapping of each observation to the subject (number 1 to 30) performing the activity
    
    - `X_test.txt` - actual observations for 561 variables from the subset of subjects
    
    - `y_test.txt` - mapping of each observation to the activity performed (number 1 to 6)

- **`train`** - folder containing the *train* subset of data (7252 observations), which contain 3 text files and 1 folder

    - `Inertial Signal` - raw signal data from the sensors, irrelevant for this project
    
    - `subject_train.txt` - mapping of each observation to the subject (number 1 to 30) performing the activity
    
    - `X_train.txt` - actual observations for 561 variables from the subset of subjects
    
    - `y_train.txt` - mapping of each observation to the activity performed (number 1 to 6)
    

## Transformations/Processing

The processing performed on the data is executed by the **`run_analysis.R`** script in this repository, and it can be divided to roughly 6 parts:

**1. Download/Unzip/Load Data**

The code checks for whether the unzip data directory already exists in the working directory. If not,  the file is downloaded from the provided link and subsequently unzipped. Next, load all of the relevant data from both the **`train`** and **`test`** folders (observations, subject mapping, and activity mapping), as well as the activity and variable labels from `activity_labels.txt` and `features.txt` files respectively.

**2. Merge `train` And `test` Subject and Activity Data**

The subject and activity data are combined into separate columns first by row binding the corresponding files (`subject_train.txt`, `subject_test.txt`, `y_train.txt`, and `y_test.txt`) from the `train` and `test` folders, and then column binding the two columns. The result is a 10299 by 2 data frame containing 1 column for subject and 1 column for activity. 

**3. Replace `activity` Data and Subsetting `mean` and `std` Data**

The numeric values (1 to 6) in `activity` column are replaced with corresponding text descriptions (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) from the mapping provided by the `activity_labels.txt` file.

The `train` and `test` observation data are combined to create a 10299 by 561 dataframe. The `grep` function is then used to find the column indices of any variable that contains "mean" or "std" (standard deviation) data. 79 variables are found. The subset of data for these 79 variables is then column bound with the subject and activity dataframe to create a 10299 by 81 dataframe.

**4. Correct Variable Names**

The existing column names for the 81 variables are taken as they are, and the following adjustments are made in order to make the variable names more precise and descriptive:

- "^f" (letter "f" at the beginning) --> "freq" which indicates frequency domain values

- "^t" (letter "t" at the beginning) --> "time" which indicates time domain values
 
- "Acc" --> "Acceleration"

- "-mean()" --> "Mean"

- "-meanFreq()" --> "MeanFrequency"

- "-std()" --> "Std" which means standard deviation

- "Gyro" --> "Gyroscope"

- "Mag" --> "Magnitude"

- "BodyBody" --> "Body"

The adjusted values are then assigned back to the processed data frame as column names.

**5. Create New Dataset with Averages of Each Variable/Activity/Subject**

The `aggregate` function is used to summarize the data by taking the mean of the other variables grouped by activity and then subject, such that each subject will have 6 rows of data averages for each of the 6  correponding activities. With 30 subjects, this ultimately creates a 180 by 81 dataframe.

**6. Output the New Dataset**

The `write.table` function is used to save the resultant 180 by 81 dataframe to a text file titled `UCI HAR Tidy Averages Dataset.txt`.

## Final Output

The final output file, `UCI HAR Tidy Averages Dataset.txt`, contains the averages for the means and standard deviation of all measurements for each subject and activity pair along with the variable names. The detailed explanation of each of the 81 variables can be found in the [`CodeBook.md`](https://github.com/sux13/Getting-CleaningDataCourseProject/blob/master/CodeBook.md) file. 