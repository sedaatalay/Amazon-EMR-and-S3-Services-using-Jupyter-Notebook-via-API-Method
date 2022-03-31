# Amazon EMR and S3 Services using Jupyter Notebook via API Method


<p> In this article we will see how to send Spark-based ETL studies to an Amazon EMR cluster.
<p> Cases:
    
- Submit the work as an EMR step using API.
    
<p></br>

## VIA API 

- Open the AWS console and navigate to the S3 service.
    
- Create an S3 bucket with folders
    - Datasets
    - logs
  
- SSH into your cluster. We can copy from the EMR summary tab.
```console
ssh -i <your-key-pair> hadoop@<emr-master-public-dns-address>    
```
<img width="926" alt="Ekran Resmi 2022-03-26 19 13 53" src="https://user-images.githubusercontent.com/91700155/160248174-cd3a6ac5-f2fa-4b97-aa4e-0759f8dccc1d.png">
  
- In the EMR terminal, run the following command to the use Pyspark.

```console
sudo pip3 install requests
python -m pip install --upgrade pip
python3 -m pip install --upgrade pip
```

```console
which python
alias python='/usr/bin/python3'
/usr/bin/python3
```

- Open the "bin" folder, then install following command.

```console
sudo pip3 install pandas	
sudo python3 -m pip install boto3
```

## OPEN JUPYTER NOTEBOOK
- Click ‘Open in Jupyter’ to open your EMR Notebook.
<img width="728" alt="Ekran Resmi 2022-03-26 20 50 06" src="https://user-images.githubusercontent.com/91700155/160251450-e72ffbcb-3bcd-4593-b0c3-778b8cfb8531.png">
   
- Create a new PySpark Notebook.
 <img width="423" alt="Ekran Resmi 2022-03-26 21 10 05" src="https://user-images.githubusercontent.com/91700155/160252021-720c9d8f-956e-4523-b9e0-f3821519de96.png">
 
- Paste the following code, and click Run.
```console
import boto3
from io import StringIO # python3; python2: BytesIO 
```
<img width="818" alt="Ekran Resmi 2022-03-26 16 10 57" src="https://user-images.githubusercontent.com/91700155/160254143-1aba8f22-a56e-4df0-bd0e-adb318acbd9f.png">

```console   
import datetime
import csv
import requests
import pandas as pd

ts_begin = int(datetime.datetime.now().timestamp())
checker = 0
currency = 'BTCUSDT'
df = pd.DataFrame(columns = ['t', 'h', 'o', 'l', 'c', 'v'])


while True:
    try:
        end = f"&resolution=1&symbol={currency}&to={ts_begin}"
        #print(ts_begin)
        ts_begin -= 604860

        start = f"https://graph-api.btcturk.com/v1/klines/history?from={ts_begin}"

        #print(ts_begin)
        uri = start + end
        #print(uri)

        result = requests.get(url=uri)
        result = result.json()

        del result['s']

        for k,v in result.items():
            result[k] = v[::-1]
            
        result_df = pd.DataFrame(result)
        
        df = df.append(result_df, ignore_index=True)
        print(df.shape)
    except:
        print('AAA')
        break
        
bucket = 'myawsbucket-emr' 
csv_buffer = StringIO()
df.to_csv(csv_buffer)
s3_resource = boto3.resource('s3')
s3_resource.Object(bucket, f'Dataset/{currency}.csv').put(Body=csv_buffer.getvalue())
```  
<img width="817" alt="Ekran Resmi 2022-03-26 16 11 09" src="https://user-images.githubusercontent.com/91700155/160254148-05197c94-ec7e-4bcc-919e-267c35cf1a0c.png">
<img width="815" alt="Ekran Resmi 2022-03-26 16 11 17" src="https://user-images.githubusercontent.com/91700155/160254180-79322a7f-82ce-4f51-b8eb-58f24b3fe695.png">
<img width="813" alt="Ekran Resmi 2022-03-26 16 11 29" src="https://user-images.githubusercontent.com/91700155/160254187-71031c7a-8df9-4f51-8371-d06ae9b9f284.png">
<img width="815" alt="Ekran Resmi 2022-03-26 16 11 36" src="https://user-images.githubusercontent.com/91700155/160254193-cbee8900-7a35-4367-8612-0391ae046237.png">
<img width="817" alt="Ekran Resmi 2022-03-26 16 11 54" src="https://user-images.githubusercontent.com/91700155/160254207-516bddf3-50ab-4ff4-aafb-7682eaadea57.png">
<img width="811" alt="Ekran Resmi 2022-03-26 16 12 28" src="https://user-images.githubusercontent.com/91700155/160254222-a2e91b92-5aaa-4cd5-928e-8931b3ed69f1.png">
<img width="794" alt="Ekran Resmi 2022-03-26 16 13 10" src="https://user-images.githubusercontent.com/91700155/160254237-1bdd8583-0f73-439e-ba1a-2e2f8c3a6540.png">


## Summary of the chapter:

- Read CSV data via API method
- Write updated data back to Amazon S3 in csv format

<img width="886" alt="Ekran Resmi 2022-03-26 16 13 34" src="https://user-images.githubusercontent.com/91700155/160254253-b2638c63-babe-49bd-b58c-9291518424e0.png">


 

    




    
    
 
    
    
<p></br>
<p></br>
    
 Thank you :)
        
 
<p></br>
    
 Seda Atalay.

