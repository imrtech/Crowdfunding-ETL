# Crowdfunding-ETL

## Overview

The purpose of this project is to organize, clean and group campaign data using Python, and PostgreSQL.

## Analysis
We created an ERD to analyze the relationships between the different sets of information. We included a backers dataset and added it to our schema.

![This is an image](/crowdfunding_db_relationships.png)

## Deliverable 1: Extract

We performed ETL on the backer_info.csv dataset in Python by following the Python Dictionary Method.

```# Get the backers_info from the crowdfunding_info sheet. 
pd.set_option('max_colwidth', 400)
csv_file = "backer_info.csv"
backer_info_df = pd.read_csv(csv_file)
backer_info_df.head()
```
![This is an image](/backer_info_image.png)

```
# Iterate through the backers DataFrame and convert each row to a dictionary.
dict_values = []

for i, row in backer_info_df.iterrows():
    # Iterate through each dictionary (row) and get the values for each row using list comprehension.
    data = row['backer_info']
    converted_data = json.loads(data)
    row_values = [x for y, x in converted_data.items()]
    
    # Append the list of values for each row to a list. 
    dict_values.append(row_values)

# Print out the list of values for each row.
dict_values
```

A backers_df DataFrame with columns 'backer_id', 'cf_id', 'name', and 'email' was created.

```
# Create a backers_df DataFrame with the following columns: 'backer_id','cf_id', 'name', and 'email' 
# using the list of values lists. 
backers_df = pd.DataFrame(dict_values, columns=['backer_id', 'cf_id', 'name', 'email'])
backers_df
```
![This is an image](/backers_df_image.png)

```
# Export the DataFrame as a CSV file using encoding='utf8'.
backers_df.to_csv('backers_info_df.csv', encoding='utf8', index=False)
```

## Deliverable 2: Transform and Clean Data

```
# Check data types.
backers_df.dtypes

backer_id    object
cf_id         int64
name         object
email        object
dtype: object

```

Next we split the first name and last name columns.
```
# Split the "name" column into "first_name" and "last_name" columns.
backers_df[["first_name","last_name"]] = backers_df["name"].str.split(' ', n=1, expand=True)
backers_df
```
![This is an image](/backers_df_split.png)


Now that we have the first name and last name columns, we no longer need the name column.
```
#  Drop the name column
backers_df.drop(['name'], axis=1)

# Reorder the columns
backers_df = backers_df[['backer_id', 'cf_id', 'first_name', 'last_name', 'email']]
backers_df
``

![This is an image](/backers_df_name_dropped.png)

```
# Export the DataFrame as a CSV file using encoding='utf8'.
backers_df.to_csv('backers.csv', encoding='utf8', index=False)
```

## Deliverable 3: Create an ERD and a Table Schema and Load the Data

A table was created for the backers dataset. The schema was updated to include the new relationship between the campaign table and the backers table. The foreign key is the cf_id.