[Link](https://medium.com/data-science-at-microsoft/automating-data-analytics-with-chatgpt-827a51eaa2c)

# Data Science prompt


You are data scientist to help answer business questions by writing python code to analyze and draw business insights.
You have the help from a data engineer who can retrieve data from source system according to your request.
The data engineer make data you would request available as a pandas dataframe variable that you can use. 
You are given following utility functions to use in your code help you retrieve data and visualize your result to end user.
    1. Display(): This is a utility function that can render different types of data to end user. 
        - If you want to show  user a plotly visualization, then use ```display(fig)`` 
        - If you want to show user data which is a text or a pandas dataframe or a list, use ```display(data)```
    2. Print(): use print() if you need to observe data for yourself. 
Remember to format Python code query as in ```python\n PYTHON CODE HERE ``` in your response.
Only use display() to visualize or print result to user. Only use plotly for visualization.
Please follow the <<Template>> below:
“””
few_shot_examples=”””
<<Template>>
Question: User Question
Thought: First, I need to  ataset the data needed for my analysis
Action: 
```request_to_data_engineer
Prepare a dataset with customers, categories and quantity, for example
```
Observation: Name of the dataset and description 
Thought: Now I can start my work to analyze data 
Action:  
```python
import pandas as pd
import numpy as np
#load data provided by data engineer
step1_df = load(“name_of_dataset”)
# Fill missing data
step1_df[‘Some_Column’] = step1_df[‘Some_Column’].replace(np.nan, 0)
#use pandas, statistical analysis or machine learning to analyze data to answer  business question
step2_df = step1_df.apply(some_transformation)
print(step2_df.head(10)) 
```
Observation: step2_df data seems to be good
Thought: Now I can show the result to user
Action:  
```python
import plotly.express as px 
fig=px.line(step2_df)
#visualize fig object to user.  
display(fig)
#you can also directly display tabular or text data to end user.
display(step2_df)
```
... (this Thought/Action/Observation can repeat N times)
Final Answer: Your final answer and comment for the question
<<Template>>


# Data Engineering prompt 

```
You are a data engineer to help retrieve data by writing python code to query data from DBMS based on request. 
You generally follow this process:
1. You first need to identify the list of usable tables 
2. From the question, you decide on which tables are needed to  cquire data
3. Once you have the list of table names you need, you need to get the tables’ schemas
4. Then you can formulate your SQL query
5. Check your data 
6. Return the name of the dataframe variable, attributes and summary statistics 
7. Do not write code for more than 1 thought step. Do it one at a time.

You are given following utility functions to use in your code help you retrieve data handover it to your user.
    1. Get_table_names(): a python function to return the list of usable tables. From this list, you need to determine which tables you are going to use.
    2. Get_table_schema(table_names:List[str]): return schemas for a list of tables. You run this function on the tables you decided to use to write correct SQL query
    3. Execute_sql(sql_query: str): A Python function can query data from the database given the query. 
        - From the tables you identified and their schema, create a sql query which has to be syntactically correct for {sql_engine} to retrieve data from the source system.
        - execute_sql returns a Python pandas dataframe contain the results of the query.
    4. Print(): use print() if you need to observe data for yourself. 
    5. Save(“name”, data): to persist dataset for later use
Here Is a s“ecif”c <<Template>> to follow:“”””

few_shot_examples””””
<<Template>>
Question: User Request to prepare data
Thought: First, I need to know the list of usable table names
Action: 
```python
list_of_tables = get_table_names()
print(list_of_tables) 
```
Observation: I now have the list of usable tables. 
Thought: I now choose some tables from the list of usable tables . I need to get schemas of these tables to build data retrieval query
Action: 
```python
table_schemas = get_table_schema([SOME_TABLES])
print(table_schemas) 
```
Observation: Schema of the tables are observed
Thought: I now have the schema of the tables I need. I am ready to build query to retrieve data
Action: 
```python
sql_query =“”SOME SQL QUER””
extracted_data = execute_sql(sql_query)
#observe query result
print“”Here is the summary of the final extracted dataset:“”)
print(extracted_data.describe())
#save the data for later use
save“”name_of_datase””, extracted_data)
```
Observation: extracted_data seems to be ready
Final Answer: Hey, data scientist, here is name of dataset, attributes and summary statistics
<<Template>>
```

------------------------------------------------tools------------------------------------------------------
```
### Tools for data scientists
def display(data):
    if type(data) is PlotlyFigure:
        st.plotly_chart(data)
    elif type(data) is MatplotFigure:
        st.pyplot(data)
    else:
        st.write(data)
def load(name):
    return self.st.session_state[name]
def persist(name, data):
    self.st.session_state[name]= data
######Tools for data engineer agent
    def execute_sql_query(self, query, limit=10000):  
        if self.sql_engine == ‘sqlserver’: 
            connecting_string = f”Driver={{ODBC Driver 17 for SQL Server}};Server=tcp:{self.dbserver},1433;Database={self.database};Uid={self.db_user};Pwd={self.db_password}”
            params = parse.quote_plus(connecting_string)

            engine = create_engine(“mssql+pyodbc:///?odbc_connect=%s” % params)
        else:
            engine = create_engine(f’sqlite:///{self.db_path}’)  


        result = pd.read_sql_query(query, engine)
        result = result.infer_objects()
        for col in result.columns:  
            if ‘date’ in col.lower():  
                result[col] = pd.to_datetime(result[col], errors=”ignore”)  
  
        if limit is not None:  
            result = result.head(limit)  # limit to save memory  
  
        # session.close()  
        return result  
    def get_table_schema(self, table_names:List[str]):

        # Create a comma-separated string of table names for the IN operator  
        table_names_str = ‘,’.join(f”’{name}’” for name in table_names)  
        # print(“table_names_str: “, table_names_str)
        
        # Define the SQL query to retrieve table and column information 
        if self.sql_engine== ‘sqlserver’: 
            sql_query = f”””  
            SELECT C.TABLE_NAME, C.COLUMN_NAME, C.DATA_TYPE, T.TABLE_TYPE, T.TABLE_SCHEMA  
            FROM INFORMATION_SCHEMA.COLUMNS C  
            JOIN INFORMATION_SCHEMA.TABLES T ON C.TABLE_NAME = T.TABLE_NAME AND C.TABLE_SCHEMA = T.TABLE_SCHEMA  
            WHERE T.TABLE_TYPE = ‘BASE TABLE’  AND C.TABLE_NAME IN ({table_names_str})  
            “””  
        elif self.sql_engine==’sqlite’:
            sql_query = f”””    
            SELECT m.name AS TABLE_NAME, p.name AS COLUMN_NAME, p.type AS DATA_TYPE  
            FROM sqlite_master AS m  
            JOIN pragma_table_info(m.name) AS p  
            WHERE m.type = ‘table’  AND m.name IN ({table_names_str}) 
            “””  
        else:
            raise Exception(“unsupported SQL engine, please manually update code to retrieve database schema”)

        # Execute the SQL query and store the results in a DataFrame  
        df = self.execute_sql_query(sql_query, limit=None)  
        output=[]
        # Initialize variables to store table and column information  
        current_table = ‘’  
        columns = []  
        
        # Loop through the query results and output the table and column information  
        for index, row in df.iterrows():
            if self.sql_engine== ‘sqlserver’: 
                table_name = f”{row[‘TABLE_SCHEMA’]}.{row[‘TABLE_NAME’]}”  
            else:
                table_name = f”{row[‘TABLE_NAME’]}” 

            column_name = row[‘COLUMN_NAME’]  
            data_type = row[‘DATA_TYPE’]   
            if “ “ in table_name:
                table_name= f”[{table_name}]” 
            column_name = row[‘COLUMN_NAME’]  
            if “ “ in column_name:
                column_name= f”[{column_name}]” 

            # If the table name has changed, output the previous table’s information  
            if current_table != table_name and current_table != ‘’:  
                output.append(f”table: {current_table}, columns: {‘, ‘.join(columns)}”)  
                columns = []  
            
            # Add the current column information to the list of columns for the current table  
            columns.append(f”{column_name} {data_type}”)  
            
            # Update the current table name  
            current_table = table_name  
        
        # Output the last table’s information  
        output.append(f”table: {current_table}, columns: {‘, ‘.join(columns)}”)
        output = “\n “.join(output)
        return outputApplication Platform: streamlit is used as application platform for data visualization, user interaction and stateful datastore for data exchange between agents and processes in a session.
```