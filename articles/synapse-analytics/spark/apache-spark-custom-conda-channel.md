---
title: Een aangepast Conda-kanaal maken voor pakket beheer
description: Meer informatie over het maken van een aangepast Conda-kanaal voor pakket beheer
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107683"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Een aangepast Conda-kanaal maken voor pakket beheer 
Wanneer u Python-pakketten installeert, gebruikt de Conda-pakket beheer kanalen om te zoeken naar pakketten. Mogelijk moet u om verschillende redenen een aangepast Conda-kanaal maken. U kunt bijvoorbeeld het volgende doen:

- uw werk ruimte is beveiligde gegevens exfiltration en uitgaande verbindingen worden geblokkeerd.  
- u hebt pakketten die u niet wilt uploaden naar open bare opslag plaatsen.
- u wilt am alternatieve opslag plaatsen instellen voor de gebruikers in uw werk ruimte.

In dit artikel wordt een stapsgewijze hand leiding geboden om u te helpen bij het maken van uw aangepaste Conda-kanaal binnen uw Azure Data Lake Storage-account.

## <a name="set-up-your-local-machine"></a>Uw lokale machine instellen

1. Installeer Conda op uw lokale machine. U kunt naar [Azure Synapse Spark runtime](./apache-spark-version-support.md) verwijzen om de Conda-versie te identificeren die wordt gebruikt in dezelfde runtime.
   
2. Als u een aangepast kanaal wilt maken, installeert u Conda-build.
```
conda install conda-build
```
3. Organiseer alle pakketten in voor het platform dat u wilt gebruiken. In dit voor beeld wordt Anaconda-archief op uw lokale machine geïnstalleerd.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Het opslag account koppelen aan uw computer
Vervolgens koppelen we het Azure Data Lake Storage Gen2-account op de lokale computer. Dit proces kan ook worden uitgevoerd met een WASB-account; Er wordt echter een voor beeld van het ADLSg2-account door lopen 
 
Ga naar [Deze pagina](https://github.com/Azure/azure-storage-fuse#blobfuse )voor meer informatie over het koppelen van het opslag account op uw lokale computer. 

1. U kunt blobfuse installeren vanuit de opslag plaats voor Linux-software voor micro soft-producten.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Maak uw koppel punt ( ```mkdir /path/to/mount``` ) en koppel een BLOB-container met blobfuse. In dit voor beeld gebruiken we de waarde **privatechannel** voor de variabele **mycontainer** .
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Het kanaal maken
In de volgende reeks stappen wordt een aangepast Conda-kanaal gemaakt. 

1. Maak op de lokale computer een directory om alle pakketten voor uw aangepaste kanaal te organiseren.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organiseer alle ```tar.bz2``` pakketten van https://repo.anaconda.com/pkgs/main/linux-64/ naar de submap. Zorg ervoor dat u ook alle afhankelijke tar. bz2-pakketten opneemt. 

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

Voor meer informatie kunt u ook [de Conda-gebruikers handleiding bezoeken voor het](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) maken van aangepaste kanalen. 

## <a name="storage-account-permissions"></a>Machtigingen voor het opslag account
Nu moeten de machtigingen voor het opslag account worden gevalideerd. Als u deze machtigingen wilt instellen, gaat u naar het pad waar het aangepaste kanaal wordt gemaakt. Maak vervolgens een SAS-token voor ```privatechannel``` met de machtigingen lezen, lijst en uitvoeren. 

De naam van het kanaal is nu de BLOB SAS-URL die op basis van dit proces wordt gegenereerd.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Een voor beeld van een Conda-omgevings configuratie bestand maken
Controleer ten slotte het installatie proces door een voor beeld van een Conda-bestand te maken ```environment.yml``` . Als u een werk ruimte hebt waarvoor DEP is ingeschakeld, moet u het ``nodefaults`` kanaal opgeven in uw omgevings bestand.

Hier volgt een voor beeld van een Conda-configuratie bestand:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
Wanneer u het voor beeld-Conda-bestand hebt gemaakt, kunt u een virtuele-Conda-omgeving maken. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Nu u uw aangepaste kanaal hebt gecontroleerd, kunt u het python- [groeps beheerproces](./apache-spark-manage-python-packages.md) gebruiken om de bibliotheken in uw Apache Spark pool bij te werken.

## <a name="next-steps"></a>Volgende stappen
- De standaard bibliotheken weer geven: [ondersteuning van Apache Spark-versie](apache-spark-version-support.md)
- Python-pakketten beheren: [python-pakket beheer](./apache-spark-manage-python-packages.md)

