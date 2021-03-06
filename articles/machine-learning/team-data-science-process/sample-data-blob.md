---
title: Voorbeeld gegevens in Azure Blob-opslag-team data Science process
description: Bemonsterings gegevens die zijn opgeslagen in Azure Blob-opslag door deze programmatisch te downloaden en vervolgens te bemonsteren met procedures die zijn geschreven in python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4832762a88073f4d819925659bf9078e18f60c2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76720274"
---
# <a name="sample-data-in-azure-blob-storage"></a><a name="heading"></a>Voorbeeldgegevens in Azure Blob-opslag

In dit artikel wordt beschreven welke voorbeeld gegevens zijn opgeslagen in Azure Blob-opslag door deze programmatisch te downloaden en vervolgens te bemonsteren met procedures die zijn geschreven in python.

**Waarom een voor beeld van uw gegevens?**
Als de gegevensset die u wilt analyseren groot is, is het doorgaans een goed idee om de gegevens te verlagen om deze te verminderen tot een kleinere, maar representatieve en meer beheersbare grootte. Bemonstering vereenvoudigt het leren van gegevens, het verkennen en functie-engineering. De rol van het proces Cortana Analytics is om snel een prototype van de functies voor gegevens verwerking en machine learning modellen mogelijk te maken.

Deze steekproef taak is een stap in het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Voorbeeld gegevens downloaden en verlagen
1. Down load de gegevens uit Azure Blob-opslag met behulp van de Blob service uit de volgende python-voorbeeld code: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Lees gegevens in een Panda data-frame van het bestand dat hierboven is gedownload.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Down sampling de gegevens met behulp `numpy`van `random.choice` het volgende:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

U kunt nu met het bovenstaande gegevens frame werken met het percentage van de steek proef voor verdere exploratie en het genereren van onderdelen.

## <a name="upload-data-and-read-it-into-azure-machine-learning"></a><a name="heading"></a>Gegevens uploaden en lezen in Azure Machine Learning
U kunt de volgende voorbeeld code gebruiken om de gegevens voor te bereiden en deze rechtstreeks in Azure Machine Learning te gebruiken:

1. Het gegevens frame schrijven naar een lokaal bestand
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Upload het lokale bestand naar een Azure-Blob met de volgende voorbeeld code:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Lees de gegevens van de Azure-Blob met Azure Machine Learning [gegevens importeren](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) , zoals wordt weer gegeven in de onderstaande afbeelding:

![Lees-BLOB](./media/sample-data-blob/reader_blob.png)

