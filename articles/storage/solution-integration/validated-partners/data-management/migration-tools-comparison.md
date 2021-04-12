---
title: Azure Storage-migratie hulpprogramma's vergelijking-ongestructureerde gegevens
description: Basis functionaliteit en vergelijking tussen hulpprogram ma's die worden gebruikt voor de migratie van ongestructureerde gegevens
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15daeb0e6bf320a0727d8e6ea502063a30e67ad0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231470"
---
# <a name="comparison-matrix"></a>Vergelijkings matrix

De volgende vergelijkings matrix bevat de basis functionaliteit van verschillende hulpprogram ma's die kunnen worden gebruikt voor de migratie van ongestructureerde gegevens. 

## <a name="supported-azure-services"></a>Ondersteunde Azure-services

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Oplossings naam**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Mobiliteit en migratie van gegevens](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Ondersteuning voor Azure Files (alle lagen)** | Ja                          | Ja                      | Ja            | Ja                            |
| **Ondersteuning voor Azure NetApp Files**      | Nee                           | Ja                      | Ja            | Ja                            |
| **Hot/cool-ondersteuning van Azure Blob**   | Nee                           | Ja (via NFS preview)    | Ja            | Ja                            |
| **Ondersteuning voor Azure Blob-archief tier** | Nee                           | Nee                       | Nee             | Ja (als migratie bestemming) |
| **Ondersteuning voor Azure Data Lake Storage** | Nee                           | Nee                       | Nee             | Nee                             |
| **Ondersteunde bronnen**      | Windows Server 2012 R2 en up | NAS-& Cloud bestands systemen | Elke NAS en S3 | NAS, blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Ondersteunde protocollen (bron/doel)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Oplossings naam**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobiliteit en migratie van gegevens](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Ja | Ja | Ja | Ja |
| **SMB 3.0**       | Ja | Ja | Ja | Ja |
| **SMB 3,1**       | Ja | Ja | Ja | Ja |
| **NFS v3**        | Nee  | Ja | Ja | Ja |
| **NFS v 4.1**      | Nee  | Ja | Nee  | Ja |
| **BLOB-REST API** | Nee  | Nee  | Ja | Ja |
| **S3**            | Nee  | Ja | Ja | Ja |

## <a name="extended-features"></a>Uitgebreide functies

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Oplossings naam**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobiliteit en migratie van gegevens](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID/SID opnieuw toewijzen**                   | Nee  | Ja                        | Ja | Nee                             |
| **Protocol-ACL opnieuw toewijzen**                | Nee  | Nee                         | Nee  | Nee                             |
| **DFS-ondersteuning**                           | Ja | Ja                        | Ja | Ja                            |
| **Ondersteuning voor beperking**                    | Ja | Ja                        | Ja | Ja                            |
| **Uitsluitingen van bestands patronen**               | Nee  | Ja                        | Ja | Ja (met kopieer functionaliteit) |
| **Ondersteuning voor selectieve bestands kenmerken** | Ja | Ja                        | Ja | Ja (voor uitgebreide kenmerken)  |
| **Doorgifte verwijderen**                   | Ja | Ja                        | Ja | Ja                            |
| **NTFS-koppelingen volgen**                 | Nee  | Ja                        | Nee  | Ja                            |
| **Eigenaar van SMB-eigenaar en-groep overschrijven**    | Ja | Ja                        | Ja | Nee                             |
| **Keten van Bewaar rapportage**            | Nee  | Ja                        | Nee  | Ja                            |
| **Ondersteuning voor alternatieve gegevens stromen**    | Nee  | Ja                        | Ja | Nee                             |
| **Planning voor migratie**              | Nee  | Ja                        | Ja | Ja                            |
| **ACL behouden**                        | Nee  | Ja                        | Ja | Ja                            |
| **Ondersteuning voor DACL**                          | Ja | Ja                        | Ja | Ja                            |
| **SACL-ondersteuning**                          | Ja | Ja                        | Ja | Nee                             |
| **Toegangs tijd behouden**                | Ja | Ja                        | Ja | Ja                            |
| **Gewijzigde tijd behouden**              | Ja | Ja                        | Ja | Ja                            |
| **Aanmaak tijd behouden**              | Nee  | Ja                        | Ja | Ja                            |
| **Ondersteuning voor Azure Data Box**       | Ja | Ja                        | Nee  | Nee                             |
| **Migratie van moment opnamen**                | Nee  | Handmatig                     | Ja | Nee                             |
| **Ondersteuning voor symbolische koppelingen**                 | Nee  | Ja                        | Nee  | Ja                            |
| **Ondersteuning voor vaste koppelingen**                     | Nee  | Gemigreerd als afzonderlijke bestanden | Ja | Ja                            |
| **Ondersteuning voor open/vergrendelde bestanden**       | Ja | Ja                        | Ja | Ja                            |
| **Incrementele migratie**                 | Ja | Ja                        | Ja | Ja                            |
| **Ondersteuning voor overschakelen**                    | Nee  | Ja                        | Ja | Nee (alleen hand matig)               |
| **[Andere functies](#other-features)**         | [Koppeling](#azure-file-sync)| [Koppeling](#dobimigrate) | [Koppeling](#data-mobility-and-migration) | [Koppeling](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Beoordeling en rapportage

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Oplossings naam**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobiliteit en migratie van gegevens](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capaciteit**                        | Nee      | Ja | Ja | Ja            |
| **aantal bestanden/mappen**            | Nee      | Ja | Ja | Ja            |
| **Leeftijds verdeling in de loop van de tijd**      | Nee      | Ja | Ja | Ja            |
| **Toegangs tijd**                     | Nee      | Ja | Ja | Ja            |
| **Tijdstip gewijzigd**                   | Nee      | Ja | Ja | Ja            |
| **Aanmaak tijd**                   | Nee      | Ja | Ja | Ja (alleen SMB) |
| **Status van rapport per bestands/object** | Gedeeltelijk | Ja | Ja | Ja            |

## <a name="licensing"></a>Licentieverlening

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Oplossings naam**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Mobiliteit en migratie van gegevens](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL (Bring Your Own License)**             | n.v.t. | Ja | Ja | Ja |
| **Azure-toezeg ging** | Ja   | Ja | Ja | Ja |

## <a name="other-features"></a>Andere functies

### <a name="azure-file-sync"></a>Azure File Sync

- Interne hash-validatie

### <a name="dobimigrate"></a>DobiMigrate

- Voorafgaande controles van de migratie
- Migratie planning
- Droge uitvoering voor knippen over testen
- Detectie en waarschuwing voor gebruikers activiteiten aan de doel zijde voordat ze worden geknipt
- Door beleid gestuurde migraties
- Geplande kopieën van herhalingen
- Configureer bare opties voor het verwerken van de beveiliging van de hoofdmap
- Verificatie op aanvraag wordt uitgevoerd
- Verificatie van de gegevens voor het lezen van de bron en het doel
- Grafische, interactieve werk stroom voor fout afhandeling
- De mogelijkheid om bepaalde bewerkingen uit te voeren, zoals verwijderingen en updates
- De mogelijkheid om de toegangs tijd op de bron te bewaren (naast bestemming)
- Mogelijkheid om terugdraai bewerkingen uit te voeren op de bron tijdens het overschakelen van de migratie
- Mogelijkheid om geselecteerde SMB-bestands kenmerken te migreren
- Mogelijkheid om NTFS-security descriptors op te schonen
- Mogelijkheid om NFSv3-machtigingen te negeren en nieuwe modus bits te schrijven naar doel
- Mogelijkheid om NFSv3 POSIX Draft-ACL'S te converteren naar NFSv4 ACL'S
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Mobiliteit en migratie van gegevens

- Hash-validatie

### <a name="intelligent-data-management"></a>Intelligente Gegevensbeheer

- Migraties op basis van een project/map
- Automatisch opnieuw proberen van fouten
- Beoordeling/rapportage: bestands typen, bestands grootte, op project gebaseerd
- Beoordeling/rapportage: aangepaste zoek opdrachten op basis van meta gegevens
- Volledige beheer oplossing voor gegevens levenscyclus voor archivering, replicatie, analyses
- Op tijd gebaseerde analyses op blob, S3-gegevens
- Taggen
- Ondersteuning voor 24 x 7 x 365
- Hash-validatie

*De lijst is voor het laatst gecontroleerd op maart 2021.*

## <a name="see-also"></a>Zie ook

- [Overzicht van opslag migratie](../../../common/storage-migration-overview.md)
- [Een Azure-oplossing voor gegevensoverdracht kiezen](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migreren naar Azure-bestandsshares](/azure/storage/files/storage-files-migration-overview)
- [Migreren naar Data Lake Storage met WANdisco LiveData platform voor Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Gegevens kopiëren of verplaatsen naar Azure Storage met AzCopy](https://aka.ms/azcopy)
- [Grote gegevens sets migreren naar Azure Blob Storage met AzReplicate (voorbeeld toepassing)](https://github.com/Azure/AzReplicate/tree/master/)
