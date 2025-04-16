# Get-started-with-Azure-Stream-Analytics

This project demonstrates how to process real-time event data using **Azure Stream Analytics**. You will provision necessary Azure resources, simulate real-time event data using **Azure Event Hubs**, process the data using a **Stream Analytics job**, and store the results in **Azure Blob Storage**.

## Overview

- Provision Azure resources (Event Hubs, Storage Account)
- Simulate real-time sales order data
- Create and configure an Azure Stream Analytics job
- Define input and output for the job
- Write a query to aggregate data in 10-second intervals
- Store results in Blob Storage
- Visualize the output JSON records

## Prerequisites

- Azure Subscription
- Azure CLI or Azure Cloud Shell
- Basic knowledge of Azure Event Hubs and Stream Analytics

---

## Step 1: Provision Azure Resources

1. Run the provided setup script in **Azure Cloud Shell**:
   ```bash
   ./setup.sh

### Step 2: Simulate Real-Time Data

node ~/dp-203/Allfiles/labs/17/orderclient

### Step 3: Create a Stream Analytics Job
In the Azure portal, go to the dp203-xxxxxxx resource group and create a Stream Analytics Job:

 - Name: process-orders

 - Region: Same as other resources

 - Streaming Units: 1

 - Hosting: Cloud

### Step 4: Configure Input (Event Hub)
In the job overview, select Add Input > Stream input > Event Hub:

 - Alias: orders

 - Select your Event Hub namespace and event hub (e.g. eventhubxxxxxxx)

 - Consumer group: $Default

 - Authentication: Managed Identity (System-assigned)

 - Format: JSON, UTF-8

### Step 5: Configure Output (Blob Storage)
Go to Outputs > Add > Blob Storage/ADLS Gen2:

 - Alias: blobstore

 - Select your storage account (e.g. storexxxxxxx) and the data container

 - Authentication: Managed Identity

 - Serialization: JSON, Line-separated, UTF-8

 - Write Mode: Append

 - Path Pattern: {date} (Format: YYYY/MM/DD)

 - Min Rows: 20, Max Time: 1 min

### Step 6: Write and Save the Query
On the Query page, replace the default with:

SELECT
    DateAdd(second,-10,System.TimeStamp) AS StartTime,
    System.TimeStamp AS EndTime,
    ProductID,
    SUM(Quantity) AS Orders
INTO
    [blobstore]
FROM
    [orders] TIMESTAMP BY EventProcessedUtcTime
GROUP BY ProductID, TumblingWindow(second, 10)
HAVING COUNT(*) > 1

### Step 7: Start the Job
On the job overview page, click Start to begin processing.

Return to Cloud Shell and run:
node ~/dp-203/Allfiles/labs/17/orderclient
 - Sends another 1000 simulated sales orders

### Step 8: View Results in Blob Storage
1. Go to the Storage Account > Containers > data

2. Navigate through the folder structure:

 - /YYYY/MM/DD/HH/

 - Files like: 0_xxxxxxxxxx.json

 - Click ... > View/Edit to see records like:

  {
  "StartTime": "2022-11-23T18:16:25Z",
  "EndTime": "2022-11-23T18:16:35Z",
  "ProductID": 1,
  "Orders": 16.0
}

### Step 9: Stop the Job
Go to the Stream Analytics job page and click Stop

### Step 10: Clean Up
To avoid incurring charges, delete the resource group

👤 Yazar
Sefa Öztürk
BT Stajyeri | Azure Veri Mühendisi devam ediyor
📇 LinkedIn: https://www.linkedin.com/in/sefa-ozturk1972
