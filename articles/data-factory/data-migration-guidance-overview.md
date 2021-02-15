---
title: Gegevens migreren van data Lake en Data Warehouse naar Azure
description: Gebruik Azure Data Factory om gegevens van uw data Lake en Data Warehouse te migreren naar Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4e7421cb8ea4b0c14e4f4b59a688cdb1afe7d462
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367704"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Als u uw data Lake of ENTER prise Data Warehouse (EDW) wilt migreren naar Microsoft Azure, kunt u overwegen Azure Data Factory te gebruiken. Azure Data Factory is heel geschikt voor de volgende scenario's:

- Migratie van een Big Data-werk belasting van de Amazon Simple Storage-service (Amazon S3) of een on-premises Hadoop Distributed File System (HDFS) naar Azure
- EDW migratie van Oracle Exadata, Netezza, Teradata of Amazon Redshift naar Azure

Azure Data Factory kunt PETA bytes (PB) met gegevens voor data Lake Migration verplaatsen en tien tallen terabytes (TB) aan gegevens voor Data Warehouse migraties.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Waarom Azure Data Factory kunnen worden gebruikt voor gegevens migratie

- Met Azure Data Factory kunt u eenvoudig de hoeveelheid verwerkings kracht schalen om gegevens op een server te verplaatsen met hoge prestaties, flexibiliteit en schaal baarheid. En u betaalt alleen voor wat u gebruikt. Let ook op het volgende: 
  - Azure Data Factory heeft geen beperkingen op het gegevens volume of het aantal bestanden.
  - Azure Data Factory kunt uw netwerk-en opslag bandbreedte volledig gebruiken om de maximale door Voer van gegevens verplaatsing in uw omgeving te behaalen.
  - Azure Data Factory maakt gebruik van een betalen per gebruik-methode, zodat u alleen betaalt voor de tijd die u daad werkelijk gebruikt voor het uitvoeren van de gegevens migratie naar Azure.  
- Azure Data Factory kunt een eenmalige historische belasting en geplande incrementele belastingen uitvoeren.
- Azure Data Factory gebruikt Azure Integration runtime (IR) om gegevens te verplaatsen tussen openbaar toegankelijke data Lake-en Warehouse-eind punten. Het kan ook zelf-hostende IR gebruiken voor het verplaatsen van gegevens voor data Lake-en Warehouse-eind punten in azure Virtual Network (VNet) of achter een firewall.
- Azure Data Factory heeft beveiliging op bedrijfs niveau: u kunt Windows Installer (MSI) of service-identiteit gebruiken voor beveiligde service-to-Service-integratie, of gebruik Azure Key Vault voor referentie beheer.
- Azure Data Factory biedt een gratis ontwerp functie voor code en een uitgebreid, ingebouwd bewakings dashboard.  

## <a name="online-vs-offline-data-migration"></a>Online versus offline gegevens migratie

Azure Data Factory is een standaard hulp programma voor het migreren van gegevens via een netwerk (Internet, er of VPN). Met offline gegevens migratie verzenden gebruikers fysiek apparaten voor gegevens overdracht van hun organisatie naar een Azure-Data Center.  

Er zijn drie belang rijke aandachtspunten wanneer u kiest tussen een online-en offline migratie methode:  

- Grootte van de gegevens die moeten worden gemigreerd
- Netwerkbandbreedte
- Migratie venster

Stel dat u van plan bent om Azure Data Factory te gebruiken voor het volt ooien van de gegevens migratie binnen twee weken (uw *migratie venster*). Let op de regel roze/blauw knippen in de volgende tabel. In de laagste roze cel voor een bepaalde kolom ziet u de gegevens grootte/netwerk bandbreedte koppeling waarvan het migratie venster zich het dichtst bij maar minder dan twee weken bevindt. (Elke grootte/band breedte-koppeling in een blauwe cel heeft een venster voor online migratie van meer dan twee weken.) 

![Online versus offline ](media/data-migration-guidance-overview/online-offline.png) deze tabel helpt u te bepalen of u kunt voldoen aan uw beoogde migratie venster via online migratie (Azure Data Factory) op basis van de grootte van uw gegevens en de beschik bare netwerk bandbreedte. Als het venster voor online migratie meer dan twee weken duurt, moet u offline migratie gebruiken.

> [!NOTE]
> Door online migratie te gebruiken, kunt u zowel historische gegevens belasting als incrementele feeds end-to-end uitvoeren met één hulp programma.  Met deze aanpak kunnen uw gegevens worden gesynchroniseerd tussen de bestaande Store en de nieuwe Store tijdens het hele migratie venster. Dit betekent dat u uw ETL-logica opnieuw kunt samen stellen in de nieuwe opslag met vernieuwde gegevens.


## <a name="next-steps"></a>Volgende stappen

- [Gegevens migreren van AWS S3 naar Azure](data-migration-guidance-s3-azure-storage.md)
- [Gegevens migreren van een on-premises Hadoop-cluster naar Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Gegevens migreren van een on-premises Netezza-server naar Azure](data-migration-guidance-netezza-azure-sqldw.md)
