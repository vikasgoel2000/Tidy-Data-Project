Code Book
=========

For an explanation of the features involved, see `features_info.txt` in the given data set.

Variables
---------
* `data` - Data frame, 10299 observations, 564 variables
    * `activityNumber` - The outcome (y). It is a numeric representation of the activity being performed in each observation.
    * `activity` - Labels naming the activity, as described in the given data.
    * `subject` - ID number of the subject.
    * The remaining 561 columns are numeric values of the features (X), named as described in the given data.
* `meansDevs` - Data frame, 10299 observations, 68 variables
	* `activity` - Labels naming the activity.
	* `subject` - ID number of the subject.
	* The remaining 66 columns correspond to the numeric values of features containing `-mean()` or `-std()` in their names. Their names have been cleaned up to be self-explanatory.
* `dataSummary` - Data frame, 180 observations, 68 variables
	* `activity` - Labels naming the activity.
	* `subject` - ID number of the subject.
	* The remaining 66 columns correspond to the means of their counterparts in `meansDevs`, for each subject, grouped by activity.

Process
-------
1. The training case subjects, features, and outcomes are read, then aggregated with `cbind` into `traindata`. The same is done for test cases, aggregated into `testdata`.
2. `testdata` and `traindata` are aggregated with `rbind` to form `data`.
2. Feature names are read from `features.txt` into `features` and used to set column names for `data`.
3. Activity labels are read from `activity_labels.txt` into `activities` and this data frame is merged with `data` on the basis of `activityNumber`. This adds an `activity` column providing a description of activities instead of a numeric representation.
4. `activity`, `subject`, and all columns with names containing `-mean()` or `-std()` are extracted from data. This is done using `grep` on a regexp. The resulting data frame is stored as `meansDevs`.
5. Column names in `meansDevs` are cleaned to be more readable and meaningful, using `gsub` on regexps to replace parts of column names with descriptive equivalents.
6. `meansDevs` is `melt`ed with `activity` and `subject` as IDs. It is then `dcast`ed to give means of each feature. The result is stored in `dataSummary`.
7. `dataSummary` is written to `data_summary.txt` in the `UCI HAR Dataset` folder.

Points of note
--------------
* For `meansDevs` and `dataSummary`, the cryptic feature names are modified into more readable and meaningful [camelCase](http://en.wikipedia.org/wiki/CamelCase) names.
* The activity labels are not of the character class but of the factor class, for easier grouping operations in the future. A side effect of this is that underscores have been left in (eg. `WALKING_UPSTAIRS` instead of `WALKING UPSTAIRS`) because using spaces would make factor levels confusing, as follows:

    ```
    > head(dataSummary$activity)
    [1] LAYING LAYING LAYING LAYING LAYING LAYING
    Levels: LAYING SITTING STANDING WALKING WALKING DOWNSTAIRS WALKING UPSTAIRS
    ```
* Because the large size of raw data involved in this project may potentially cause slowdown, intermediary objects that are no longer needed are removed with `rm()`.