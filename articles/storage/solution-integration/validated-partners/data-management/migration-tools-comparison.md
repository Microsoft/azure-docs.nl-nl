---
title: Azure Storage vergelijking van hulpprogramma's voor migratie - Niet-gestructureerde gegevens
description: Basisfunctionaliteit en vergelijking tussen hulpprogramma's die worden gebruikt voor de migratie van ongestructureerde gegevens
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 862feace6aab4f49ad3482c4ccd6510669c876a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576530"
---
# <a name="comparison-matrix"></a>Vergelijkingsmatrix

De volgende vergelijkingsmatrix toont de basisfunctionaliteit van verschillende hulpprogramma's die kunnen worden gebruikt voor de migratie van ongestructureerde gegevens. 

## <a name="supported-azure-services"></a>Ondersteunde Azure-services

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Naam van de oplossing**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Gegevensmobiliteit en migratie](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files ondersteuning (alle lagen)** | Ja                          | Ja                      | Ja            | Ja                            |
| **Azure NetApp Files ondersteuning**      | Nee                           | Ja                      | Ja            | Ja                            |
| **Ondersteuning voor Azure Blob Hot/Cool**   | No                           | Ja (via NFS-preview)    | Ja            | Ja                            |
| **Ondersteuning voor Azure Blob Archive-laag** | Nee                           | Nee                       | Nee             | Ja (als migratiebestemming) |
| **Azure Data Lake Storage ondersteuning** | Nee                           | Nee                       | Nee             | Nee                             |
| **Ondersteunde bronnen**      | Windows Server 2012 R2 en hoger | NAS & cloudbestandssystemen | NaS en S3 | NAS, Blob, S3                  |

## <a name="supported-protocols-source--destination"></a>Ondersteunde protocollen (bron/doel)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Gegevensdynamica](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Naam van de oplossing**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Gegevensmobiliteit en migratie](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | Ja | Ja | Ja | Ja |
| **SMB 3.0**       | Ja | Ja | Ja | Ja |
| **SMB 3.1**       | Ja | Ja | Ja | Ja |
| **NFS v3**        | Nee  | Ja | Ja | Ja |
| **NFS v4.1**      | Nee  | Ja | Nee  | Ja |
| **Blob-REST API** | Nee  | Nee  | Ja | Ja |
| **S3**            | Nee  | Ja | Ja | Ja |

## <a name="extended-features"></a>Uitgebreide functies

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Gegevensdynamica](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **Naam van de oplossing**  | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Gegevensmobiliteit en migratie](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID/SID opnieuw toe temappen**                   | Nee  | Ja                        | Ja | Nee                             |
| **Protocol-ACL opnieuw toe temappen**                | Nee  | Nee                         | Nee  | Nee                             |
| **DFS-ondersteuning**                           | Ja | Ja                        | Ja | Ja                            |
| **Ondersteuning voor beperking**                    | Ja | Ja                        | Ja | Ja                            |
| **Uitsluitingen van bestandspatronen**               | Nee  | Ja                        | Ja | Ja (met kopieerfunctionaliteit) |
| **Ondersteuning voor selectieve bestandskenmerken** | Ja | Ja                        | Ja | Ja (voor uitgebreide kenmerken)  |
| **Door propagaties verwijderen**                   | Ja | Ja                        | Ja | Ja                            |
| **NTFS-verbinding volgen**                 | Nee  | Ja                        | Nee  | Ja                            |
| **SMB-eigenaar en groepseigenaar overschrijven**    | Ja | Ja                        | Ja | Nee                             |
| **Rapportage van de keten van bewaring**            | Nee  | Ja                        | Nee  | Ja                            |
| **Ondersteuning voor alternatieve gegevensstromen**    | Nee  | Ja                        | Ja | Nee                             |
| **Planning voor migratie**              | Nee  | Ja                        | Ja | Ja                            |
| **ACL met behoud**                        | Nee  | Ja                        | Ja | Ja                            |
| **DACL-ondersteuning**                          | Ja | Ja                        | Ja | Ja                            |
| **SACL-ondersteuning**                          | Ja | Ja                        | Ja | Nee                             |
| **Toegangstijd behouden**                | Ja | Ja                        | Ja | Ja                            |
| **Gewijzigde tijd behouden**              | Ja | Ja                        | Ja | Ja                            |
| **Aanmaaktijd behouden**              | Nee  | Ja                        | Ja | Ja                            |
| **Azure Data Box ondersteuning**       | Ja | Ja                        | Nee  | Nee                             |
| **Migratie van momentopnamen**                | No  | Handmatig                     | Ja | Nee                             |
| **Ondersteuning voor symbolische koppeling**                 | Nee  | Ja                        | Nee  | Ja                            |
| **Ondersteuning voor harde koppeling**                     | No  | Gemigreerd als afzonderlijke bestanden | Ja | Ja                            |
| **Ondersteuning voor geopende/vergrendelde bestanden**       | Ja | Ja                        | Ja | Ja                            |
| **Incrementele migratie**                 | Ja | Ja                        | Ja | Ja                            |
| **Ondersteuning voor overstappen**                    | Nee  | Ja                        | Ja | Nee (alleen handmatig)               |
| **[Andere functies](#other-features)**         | [Koppeling](#azure-file-sync)| [Koppeling](#dobimigrate) | [Koppeling](#data-mobility-and-migration) | [Koppeling](#intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>Evaluatie en rapportage

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Gegevensdynamica](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Naam van de oplossing**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Gegevensmobiliteit en migratie](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Capaciteit**                        | Nee      | Ja | Ja | Ja            |
| **Aantal bestanden/mappen**            | Nee      | Ja | Ja | Ja            |
| **Leeftijdsdistributie in de tijd**      | Nee      | Ja | Ja | Ja            |
| **Toegangstijd**                     | Nee      | Ja | Ja | Ja            |
| **Wijzigingstijd**                   | Nee      | Ja | Ja | Ja            |
| **Aanmaaktijd**                   | Nee      | Ja | Ja | Ja            |
| **Status per bestand/objectrapport** | Gedeeltelijk | Ja | Ja | Ja            |

## <a name="licensing"></a>Licentieverlening

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **Naam van de oplossing**   | [Azure File Sync](/azure/storage/files/storage-sync-files-deployment-guide) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Gegevensmobiliteit en migratie](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligente Gegevensbeheer](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL (Bring Your Own License)**             | N/A | Ja | Ja | Ja |
| **Azure Commitment** | Ja   | Ja | Ja | Ja |

## <a name="other-features"></a>Andere functies

### <a name="azure-file-sync"></a>Azure File Sync

- Interne hashvalidatie

### <a name="dobimigrate"></a>DobiMigrate

- Controles vooraf voor migratie
- Migratieplanning
- Proefrun voor cut-overtests
- Gebruikersactiviteit aan de doelzijde detecteren en waarschuwen vóór de cut-over
- Door beleid gestuurde migraties
- Geplande kopieer iteraties
- Configureerbare opties voor het afhandelen van beveiliging van hoofdmaps
- Verificatie op aanvraag wordt uitgevoerd
- Verificatie van teruglezen gegevens op bron en doel
- Werkstroom voor grafische, interactieve foutafhandeling
- Mogelijkheid om te voorkomen dat bepaalde bewerkingen worden doorgegeven, zoals verwijderen en updates
- Mogelijkheid om de toegangstijd op de bron te behouden (naast het doel)
- Mogelijkheid om terugdraaien naar de bron uit te voeren tijdens migratieoverstappen
- Mogelijkheid om geselecteerde SMB-bestandskenmerken te migreren
- Mogelijkheid om NTFS-beveiligingsdescriptors op te schonen
- Mogelijkheid om NFSv3-machtigingen te overschrijven en nieuwe modusbits naar doel te schrijven
- Mogelijkheid om NFSv3 POSIX-concept-ACLS te converteren naar NFSv4 ACLS
- SMB 1 (CIFS)

### <a name="data-mobility-and-migration"></a>Gegevensmobiliteit en migratie

- Hash-validatie

### <a name="intelligent-data-management"></a>Intelligente Gegevensbeheer

- Migraties op basis van project/map
- Automatisch opnieuw proberen van fouten
- Evaluatie/rapportage: bestandstypen, bestandsgrootte, op project gebaseerd
- Evaluatie/rapportage: Aangepaste zoekopdrachten op basis van metagegevens
- Beheeroplossing voor volledige levenscyclus van gegevens voor archivering, replicatie, analyse
- Op tijd gebaseerde analyses openen voor blob-, S3-gegevens
- Taggen
- Ondersteuning voor 24 x 7 x 365
- Hash-validatie

*Lijst is voor het laatst geverifieerd op 31 maart 2021.*

## <a name="see-also"></a>Zie ook

- [Overzicht van opslagmigratie](../../../common/storage-migration-overview.md)
- [Een Azure-oplossing voor gegevensoverdracht kiezen](/azure/storage/common/storage-choose-data-transfer-solution?toc=/azure/storage/blobs/toc.json)
- [Migreren naar Azure-bestandsshares](/azure/storage/files/storage-files-migration-overview)
- [Migreren naar Data Lake Storage met WANdisco LiveData Platform for Azure](/azure/storage/blobs/migrate-gen2-wandisco-live-data-platform)
- [Gegevens kopiëren of verplaatsen naar Azure Storage met AzCopy](https://aka.ms/azcopy)
- [Grote gegevenssets migreren naar Azure Blob Storage met AzReplicate (voorbeeldtoepassing)](https://github.com/Azure/AzReplicate/tree/master/)
