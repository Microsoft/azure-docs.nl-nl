---
title: Rollen en vereisten voor Azure Data Share
description: Meer informatie over de vereiste machtigingen voor het delen en ontvangen van gegevens met behulp van Azure data share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a832c8956f7a3d4f8669209d7ed311e7555e1e75
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644246"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Rollen en vereisten voor Azure Data Share 

In dit artikel worden de rollen en machtigingen beschreven die nodig zijn om gegevens te delen en te ontvangen met behulp van de Azure data share service. 

## <a name="roles-and-requirements"></a>Functies en vereisten

Met de Azure data share-service kunt u gegevens delen zonder dat er referenties worden uitgewisseld tussen gegevens provider en Consumer. Voor het delen op basis van moment opnamen gebruikt de Azure data share-service beheerde identiteiten (voorheen bekend als MSIs) voor verificatie bij Azure-gegevens opslag. De beheerde identiteit van de Azure-gegevens share bron moet toegang krijgen tot de gegevens opslag van Azure om gegevens te kunnen lezen of schrijven.

Om gegevens te delen of te ontvangen van een Azure-gegevens archief, moet de gebruiker ten minste over de volgende machtigingen beschikken. 

* Toestemming om te schrijven naar de Azure-gegevens opslag. Normaal gesp roken bestaat deze machtiging in de rol **Inzender** .

Voor het delen van opslag en data Lake snap shot moet u ook toestemming hebben om de roltoewijzing te maken in de Azure-gegevens opslag. Normaal gesp roken is machtiging voor het maken van roltoewijzingen in de rol **eigenaar** , de rol gebruikers toegang beheerder of een aangepaste rol met *micro soft. autorisatie/roltoewijzingen/schrijf* machtiging toegewezen. Deze machtiging is niet vereist als de beheerde identiteit van de bron van de gegevens share al toegang tot de Azure-gegevens opslag heeft gekregen. Hieronder volgt een samen vatting van de rollen die zijn toegewezen aan de beheerde identiteit van de gegevens share Bron:

|**Type gegevens archief**|**Bron gegevensopslag van gegevens provider**|**Gegevens opslag van het doel van de gegevens verbruiker**|
|---|---|---|
|Azure Blob Storage| Lezer voor opslagblobgegevens | Inzender voor Storage Blob-gegevens
|Azure Data Lake gen1 | Eigenaar | Niet ondersteund
|Azure Data Lake Gen2 | Lezer voor opslagblobgegevens | Inzender voor Storage Blob-gegevens
|

Voor delen op basis van een SQL-moment opname moet een SQL-gebruiker worden gemaakt van een externe provider in Azure SQL Database met dezelfde naam als de Azure-gegevens share resource. Voor het maken van deze gebruiker is Azure Active Directory beheerders machtiging vereist. Hieronder volgt een samen vatting van de machtiging die is vereist voor de SQL-gebruiker.

|**SQL Database type**|**SQL-gebruikers machtiging voor de gegevens provider**|**SQL-gebruikers machtiging voor gegevens verbruiker**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader, db_datawriter db_ddladmin
|

### <a name="data-provider"></a>Gegevens provider
Voor het delen van opslag-en data Lake-moment opnamen kunt u een gegevensset toevoegen aan de Azure-gegevens share. de beheerde identiteit van de provider gegevens share resource moet toegang krijgen tot de Azure-bron gegevens opslag. In het geval van een opslag account krijgt de beheerde identiteit van de gegevens share bron bijvoorbeeld de rol *Storage BLOB data Reader* . Dit wordt automatisch uitgevoerd door de Azure data share-service wanneer de gebruiker gegevensset toevoegt via Azure Portal en de gebruiker de juiste machtigingen heeft. De gebruiker is bijvoorbeeld een eigenaar van het Azure-gegevens archief of is een lid van een aangepaste rol waaraan de machtiging voor *micro soft. Authorization/Role/* permission is toegewezen. 

De gebruiker kan ook de eigenaar van de Azure-gegevens opslag de beheerde identiteit van de gegevens share bron hand matig toevoegen aan het Azure-gegevens archief. Deze actie hoeft slechts één keer per gegevens share bron te worden uitgevoerd. Volg de onderstaande stappen voor het maken van een roltoewijzing voor de beheerde identiteit van de gegevens share bron hand matig.  

1. Navigeer naar de Azure-gegevens opslag.
1. Selecteer **Access Control (IAM)**.
1. Selecteer **een roltoewijzing toevoegen**.
1. Selecteer onder *rol* de rol in de bovenstaande tabel met roltoewijzingen (bijvoorbeeld voor opslag account, selecteer *BLOB-gegevens lezer voor opslag*).
1. Typ onder *selecteren* de naam van uw Azure-gegevens share-resource.
1. Klik op *Opslaan*.

Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie over de toewijzing van rollen. Als u gegevens deelt met behulp van REST-Api's, kunt u roltoewijzing maken met behulp van de API door te verwijzen naar [Azure-rollen toewijzen met behulp van de rest API](../role-based-access-control/role-assignments-rest.md). 

Voor delen op basis van een SQL-moment opname moet een SQL-gebruiker worden gemaakt van een externe provider in SQL Database met dezelfde naam als de Azure-gegevens share bron tijdens het verbinden met SQL database met behulp van Azure Active Directory-verificatie. Aan deze gebruiker moet *db_datareader* machtiging worden verleend. Een voorbeeld script met andere vereisten voor delen op basis van SQL vindt u in de [share van Azure SQL database of de Azure Synapse Analytics](how-to-share-from-sql.md) -zelf studie. 

### <a name="data-consumer"></a>Gegevens verbruiker
Als u gegevens wilt ontvangen in een opslag account, moet de beheerde identiteit van de consument gegevens share resource toegang krijgen tot het doel-opslag account. De beheerde identiteit van de gegevens share bron moet de rol van de *BLOB voor gegevens* toegang voor de opslag hebben. Dit wordt automatisch uitgevoerd door de Azure data share-service als de gebruiker een doel Storage-account opgeeft via Azure Portal en de gebruiker over de juiste machtigingen beschikt. De gebruiker is bijvoorbeeld een eigenaar van het opslag account of is een lid van een aangepaste rol die de machtiging *micro soft. Authorization/Role Assignment/write* is toegewezen heeft. 

De gebruiker kan ook eigenaar zijn van het opslag account om de beheerde identiteit van de gegevens share bron hand matig toe te voegen aan het opslag account. Deze actie hoeft slechts één keer per gegevens share bron te worden uitgevoerd. Volg de onderstaande stappen voor het maken van een roltoewijzing voor de beheerde identiteit van de gegevens share bron hand matig. 

1. Navigeer naar de Azure-gegevens opslag.
1. Selecteer **Access Control (IAM)**.
1. Selecteer **een roltoewijzing toevoegen**.
1. Selecteer onder *rol* de rol in de bovenstaande tabel met roltoewijzingen (bijvoorbeeld voor opslag account, selecteer *BLOB-gegevens lezer voor opslag*).
1. Typ onder *selecteren* de naam van uw Azure-gegevens share-resource.
1. Klik op *Opslaan*.

Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie over de toewijzing van rollen. Als u gegevens ontvangt met behulp van REST-Api's, kunt u roltoewijzing maken met behulp van de API door te verwijzen naar [Azure-rollen toewijzen met behulp van de rest API](../role-based-access-control/role-assignments-rest.md). 

Voor een op SQL gebaseerd doel moet een SQL-gebruiker worden gemaakt van een externe provider in SQL Database met dezelfde naam als de Azure-gegevens share bron tijdens het verbinden met SQL database met behulp van Azure Active Directory-verificatie. Deze gebruiker moet worden toegekend *db_datareader, db_datawriter db_ddladmin* machtiging. Een voorbeeld script met andere vereisten voor delen op basis van SQL vindt u in de [share van Azure SQL database of de Azure Synapse Analytics](how-to-share-from-sql.md) -zelf studie. 

## <a name="resource-provider-registration"></a>Registratie van resource provider 

Mogelijk moet u de resource provider micro soft. DataShare hand matig registreren in uw Azure-abonnement in de volgende scenario's: 

* De Azure data share-uitnodiging voor de eerste keer in uw Azure-Tenant weer geven
* Gegevens uit een Azure-gegevens archief delen in een ander Azure-abonnement van uw Azure-gegevens share-resource
* Gegevens ontvangen in een Azure-gegevens archief in een ander Azure-abonnement van uw Azure-gegevens share-resource

Volg deze stappen om de resource provider micro soft. DataShare te registreren bij uw Azure-abonnement. U hebt *Inzender* toegang tot het Azure-abonnement nodig om de resource provider te registreren.

1. Ga in het Azure Portal naar **abonnementen**.
1. Selecteer het abonnement dat u voor Azure-gegevens share gebruikt.
1. Klik op **resource providers**.
1. Zoek naar micro soft. DataShare.
1. Klik op **Registreren**.
 
Zie [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md)voor meer informatie over de resource provider.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over rollen in azure- [definities van Azure-rollen begrijpen](../role-based-access-control/role-definitions.md)