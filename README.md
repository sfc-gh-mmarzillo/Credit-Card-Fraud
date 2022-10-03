# Anomaly Detection with Snowflake, Snowpack and Sagemaker - Demo Instructions

https://docs.google.com/presentation/d/1ZLqb3WV2gQQYII69gUek5UE5OtMo0oBmc9_JYtwM09o/edit#slide=id.g15c63576b97_0_147

### Introduce the use case (slide 2)

The use case builds a model that predicts fraud using credit card transaction data from Patterson Consulting (publicly available data). 

This is a common use case across many verticals and often when you’re working on your first anomaly detection model you have to use an unsupervised model.

Feature engineering is an important consideration because most unsupervised models cannot weight features so it’s important to be mindful when engineering features.

### Architecture (slide 3)

The architecture starts with two datasets in Snowflake: 1. The base credit card transaction data and 2. Safegraph census data that we use to pull in and enhance the transaction data. 

With these two datasets we use Snowpark to engineer additional features then materialize a data frame in a Sagemaker notebook and build a model. 

The model is then deployed as a Sagemaker endpoint and we build a quick Sagemaker Data Wrangler data flow that accesses “new data” in Snowflake passes it to the model, generates predictions and writes them to an S3 bucket. A Snowflake pipe then automatically appends that data to a table in Snowflake.

### Data (slide 4)

You can take a look at the data, the transaction data is pretty straightforward, but with this type of data you often want to perform different types of aggregations to get the eventual model a “better chance” at picking up a signal. Additionally, we bring in census data on Median Wage in the area of the transaction to try and improve the model.

### Snowpark for Data Engineering (slide 5)

Here is a look at how the Python scripts we use to aggregate statistics on the types of transactions that are likely to occur at certain days of the week or or hours of the day (e.g. maybe if the average transaction amount in the morning hours is low, when we see a high transaction in the morning it is more likely to be fraudalent). 

Snowpark utilizes lazy evaluation, so we’re able to build the engineering steps then when we use the “.to_pandas()” method it actually executes a big SQL query in Snowflake to materialize the data.

### SQL Under the Hood (slide 6)

Here you can see the big SQL query that is executed in Snowflake when you materialize the data using Python Snowpark.

We can use this query to generate a table or a view that can be used to serve new data for inference. This is a cool feature that shows off the efficiency that can be gained from using Snowpark for feature engineering.

### Build and Deploy Random Cut Forest Model (slide 7)

Here we use the base parameters to build a RCF model and deploy it as a Sagemaker endpoint. Straightforward if you’ve ever done this in Sagemaker before.

### Data Wrangler for Inference (slide 8)

Once the model is deployed, it’s easy to spin up a Data Wrangler data flow, connect to Snowflake as a source and use a re-usable piece of Python code to generate predictions on new data then write them back down to an S3 bucket.

That bucket location will have to be set up as an external stage with a snow pipe so that the predictions will automatically be copied over to a Snowflake table.

### Quick Check on Performance (slide 9)

…and now that the predictions are in Snowflake we can take a look at the performance of the model. And it appears that the records that we’re saying are more likely to be fraudulent tend to actually be more fraudulent on average.




