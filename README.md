# ML_Capstone_Project

1	Architectural Components Overview

1.1	Data Source

The data source selected includes a csv file consisting of 51 time-stamped sensors data and machine status as the target variable.

1.1.1	Technology Choice
Since the data source is a csv file, the technology or software to interpret the data can be one of the following: 
1.	Jupyter Notebook on Watson Studio or offline based on compute resources required
2.	Anaconda Software for running python code and running algorithm on sensor data.

1.1.2	Justification
Data analysis was performed in Watson Studio using Python notebook where source file was uploaded into the cloud as it made the task easier to make the file more readable and easily available in Watson data platform.


1.2	Enterprise Data

1.2.1	Technology Choice
The data corresponds to sensor readings for a given machine. This can run in an existing datacenter or IoT enabled smart building wherein multiple sensor’s data can be correlated with the anomaly or aberrations that can occur over time. 

The data was predefined and structured, hence, there was no need to stream live data. However, for simulation purpose, node-red could be used for generation of similar data from combination of variable sensors. 

1.3	Streaming analytics
In order to initiate with performing analysis of the data, the data stream is divided into training set and test set. However, initially we need to analyze the sensor data using matlab visualization library (matplotlib). 

Data consists of 220320 entries × 55 columns wherein 1st column consists of unnamed index followed by timestamp values and then 51 sensor values which are defined as non-null float objects and followed by the target variable, which in this case is the machine status. 

Machine status can have the value of 0, 1 and 2 where:
0: Broken
1: Normal operation
2: Recovering


1.3.1	Technology Choice

1.3.2	Justification
This task involves – as the name implies – accessing the data source, transforming it in a way it can be easily worked with and finally make it available to downstream analytics processes – either real-time streaming or batch ones.

1.4	Data Integration 

1.4.1	Technology Choice
All the data and algorithm for detecting the anomaly in the machine status based on sensor data has been extracted from csv file and is running in Watson data and cloud platform. 


1.5	Data Repository

1.5.1	Technology Choice
As per the data repository is concerned, the data is stored in the Watson cloud platform and the python notebook working on the dataset is also stored in cloud for simplicity purposes.

1.6	Discovery and Exploration 

1.6.1	Technology Choice

As part of analysis via data exploration step, it is found that data consists of null values, NaN and some sensor values have zero values which would have little to no effect on the machine status (outcome) regardless of any variation in the sensor value. Using Matlab visualization chart, all the input data is visualized inline for detecting relevant  patterns in the dataset as shown below:

 
 
Some sensor values were visualized for the purpose of applying the most appropriate feature transformation step as part of data -preprocessing. 

Following graph shows variation in the sensor_01 data over timeseries pattern. 
 



Similarly, sensor_04 data was plotted at random to identify the patterns:
 


Plot of Machine Status vs Sensor values is also established in order to visualize any correlation as well. 
 


After close inspection of the data set, it is identified that sensor_15 contains only null values and hence, cannot account for any variation in the machine status. Hence, Sensor 15 column was dropped from the data frame. 

Since Unnamed index has no part in the computation of the output (machine_status), said column was deleted. Simultaneously, as index, convert timestamped column was transformed into index for simplicity as shown:

  

Once we segregate the output values, we get the following data corresponding to the machine status w.r.t. sensor values varying over time: 
NORMAL        205836
RECOVERING     14477
BROKEN             7

Then, timestamp column was deleted. 

A raw counterplot is created for machine status for better visualization of the target variable. 
 



It turns out that data is skewed so we need to use median value to fill the data. Hence, ffill method is used to fill the missing values.


Since the output is a categorical feature set, label encoder is applied to encode the machine status into 0, 1 and 2. 
# 1 - normal 
# 2 - recovering 
# 0 - broken

X and y variables are defined wherein X comprises of all the input labels i.e. sensor values varying with time and Y comprises of the machine status. 

Since we are concerned with the machine output as normal or abnormal behavior, 0 (broken) and 2 (recovering) status of the machine can be clustered into 1 category which is non-functional and 1 which is the normal state implies normal working or functional category. Hence it becomes a binary classification problem in which case logistic regression is used to try to solve the binomial classification. 

We are splitting data into training and test sets to assess model performance on the test set (or we use cross validation) in 70:30 ratio. 

All sensor values are computed over time, if further iterations in the features need to be done:
  


1.6.2	Justification
-	Scikit learn library is used with pandas for training purposes.

1.7	Actionable Insights

1.7.1	Technology Choice
Model evaluation is a critical task in data science. This is one of the few measures business stakeholders are interested in. Model performance heavily influences business impact of a data science project. Therefore, it is important to take some time apart in an independent task in the process model. 
Confusion_matrix, classification_report and accuracy_score is computed once the training data has been fit and output is compared with the test data. 

 

As can be seen from the confusion matrix, the accuracy of the model with logistic regression and fine tuning the hyperparameters is found to be 0.9975944081336238, i.e approximately 99.76%.

Output generated for the predicted test plot highlighting the machine status:
 
It can be easily compared with the test plot visualized below: 
 
Once the predicted value has been identified based on sensor correlation with machine status, we find the time corresponding to the predicted anomaly i.e. output should be date and time value when y_pred was 0 or 2 (Machine status was predicted to be abnormal). Down times are saved and the user can receive notifications or can look the anomalous events based on sensor correlation which might occur over time if similar correlated sensor data arises in the stream of data. 



