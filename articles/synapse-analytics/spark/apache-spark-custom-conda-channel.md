---
title: Aangepast Conda-kanaal maken voor pakketbeheer
description: Meer informatie over het maken van een aangepast Conda-kanaal voor pakketbeheer
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 26b6adefd2d334c9fe570bfa7e63bb06b55b9d20
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588765"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Een aangepast Conda-kanaal maken voor pakketbeheer 
Bij het installeren van Python-pakketten gebruikt conda-pakketbeheer kanalen om te zoeken naar pakketten. Mogelijk moet u om verschillende redenen een aangepast Conda-kanaal maken. U kunt bijvoorbeeld het volgende vinden:

- uw werkruimte is beveiligd met gegevens exfiltratie en uitgaande verbindingen worden geblokkeerd.  
- U hebt pakketten die u niet wilt uploaden naar openbare opslagplaatsen.
- u een alternatieve opslagplaats wilt instellen voor de gebruikers in uw werkruimte.

In dit artikel bieden we een stapsgewijse handleiding om u te helpen uw aangepaste Conda-kanaal binnen uw Azure Data Lake Storage maken.

## <a name="set-up-your-local-machine"></a>Uw lokale computer instellen

1. Installeer Conda op uw lokale computer. U kunt verwijzen naar de [Azure Synapse Spark-runtime](./apache-spark-version-support.md) om de Conda-versie te identificeren die in dezelfde runtime wordt gebruikt.
   
2. Als u een aangepast kanaal wilt maken, installeert u conda-build.
```
conda install conda-build
```
3. Organiseer alle pakketten in voor het platform dat u wilt aanbieden. In dit voorbeeld installeren we Anaconda Archive op uw lokale computer.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Het opslagaccount aan uw computer toevoegen
Vervolgens wordt het Azure Data Lake Storage Gen2 aan uw lokale computer. Dit proces kan ook worden uitgevoerd met een WASB-account; We nemen echter een voorbeeld door voor het ADLSg2-account 
 
Ga naar deze pagina voor meer informatie over het aan uw lokale computer toevoegen [van het opslagaccount.](https://github.com/Azure/azure-storage-fuse#blobfuse ) 

1. U kunt blobfuse installeren vanuit de Linux-softwareopslagplaats voor Microsoft-producten.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Maak het mountpoint ( ```mkdir /path/to/mount``` ) en bevestig een Blob-container met blobfuse. In dit voorbeeld gebruiken we de waarde **privatechannel** voor de **variabele mycontainer.**
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Het kanaal maken
In de volgende reeks stappen maken we een aangepast Conda-kanaal. 

1. Maak op uw lokale computer een map om alle pakketten voor uw aangepaste kanaal te organiseren.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organiseer alle ```tar.bz2``` pakketten van in de https://repo.anaconda.com/pkgs/main/linux-64/ subdirectory. Zorg ervoor dat u ook alle afhankelijke tar.bz2-pakketten op bevat. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Voor meer informatie kunt u ook de [Conda-gebruikershandleiding voor het](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) maken van aangepaste kanalen bezoeken. 

## <a name="storage-account-permissions"></a>Machtigingen voor opslagaccounts
Nu moeten we de machtigingen voor het opslagaccount valideren. Als u deze machtigingen wilt instellen, gaat u naar het pad waar het aangepaste kanaal wordt gemaakt. Maak vervolgens een SAS-token ```privatechannel``` voor dat lees-, lijst- en uitvoermachtigingen heeft. 

De kanaalnaam is nu de BLOB SAS-URL die op basis van dit proces wordt gegenereerd.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Een voorbeeld van een Conda-omgevingsconfiguratiebestand maken
Controleer ten laatste het installatieproces door een Conda-voorbeeldbestand te ```environment.yml``` maken. Als u een DEP-werkruimte hebt ingeschakeld, moet u het ``nodefaults`` kanaal in uw omgevingsbestand opgeven.

Hier is een voorbeeld van een Conda-configuratiebestand:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Zodra u het Conda-voorbeeldbestand hebt gemaakt, kunt u een virtuele Conda-omgeving maken. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Nu u uw aangepaste kanaal hebt geverifieerd, kunt u het [Python-poolbeheerproces](./apache-spark-manage-python-packages.md) gebruiken om de bibliotheken in uw Apache Spark bijwerken.

## <a name="next-steps"></a>Volgende stappen
- De standaardbibliotheken weergeven: [Apache Spark versieondersteuning](apache-spark-version-support.md)
- Python-pakketten beheren: [Python-pakketbeheer](./apache-spark-manage-python-packages.md)

