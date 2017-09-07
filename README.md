# k-means-stock-data
# Using k-means clustering algorithm to cluster average closing price of different company


import re
import requests
import json
import pandas as pd
import numpy as np
from sklearn.cluster import KMeans

def stock_data(stock_code):              # defining the function to retrieve stock data
    json_data = list()
    url = 'https://finance.yahoo.com/quote/%s/history?p=%s' % (stock_code, stock_code)        # modifying the url with address/paramter "stock_codee"
    fhandle = requests.get(url)                         # Getting the file handle of url using requests
    str_data = re.findall('"HistoricalPriceStore":{"prices":(.*?),"isPending"',fhandle.text)      # Extracting the content having "search_pattern" from string string "fhandle.text"
    if str_data:                       # False if str_data is empty
        json_data = json.loads(str_data[0])                       # Load the string data into json data (a collection of list/dict)
        json_data = json_data[::-1]                           # reversing the order/indexing of jsn_data
    return [item for item in json_data if not "type" in item]               # return those item(list/dict from json_data) ot containing the string "type"

def create_df(stock_code):                        # defining the function to create data frame
    json_data = stock_data (stock_code)             # calling the function to get json data of stock code
    field_name = ['close','data','high','low','open','valume']          # defining the columns name
    df_totalvolume = pd.DataFrame (json_data, columns=field_name)             # converting the json data (collection of list/dict) into data frame with columns name
    return df_totalvolume                                    # return the data frame of stock data with columns name

company_codes = ['MMM','AXP','AAPL','BA','CAT','CVX','CSCO','KO','DIS','DD']         # list of company_codes
listtemp = [0]*len(company_codes)                  # list full of zeros equal to the length of comapny_codes
for i in range(len(listtemp)):                     # iterate through the length of listtemp
    listtemp[i] = create_df(company_codes[i]).close         # calling the function create_df with company codes and then store the content of close column into listtemp

status = [0]*len(company_codes)     # a list of zeros equal to the length of company codes
for i in range(len(status)):        # iterate through the length of status
    status[i] = np.sign(np.diff(listtemp[i]))              #  calculating the difference of values in close column of a company and store the sign of difference as status
kmeans = KMeans(n_clusters = 3).fit(status)              # calculating the kmean of status using "fit" with 3 clusters
pred = kmeans.predict (status)   # using the clustering of kmeans to predict the list of "status"
print (pred)
