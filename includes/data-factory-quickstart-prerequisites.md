---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: include
ms.custom: include file
ms.date: 06/27/2019
ms.openlocfilehash: 0c21c576cbb99ccc96bf7edf733769a9b28b9344
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104612400"
---
## <a name="prerequisites"></a>Vereisten

### <a name="azure-subscription"></a>Azure-abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

### <a name="azure-roles"></a>Azure-rollen

Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol *Inzender* of *Eigenaar*, of moet dit een *beheerder* van het Azure-abonnement zijn. Als u de machtigingen wilt bekijken die u binnen het abonnement hebt, gaat u naar [Azure Portal](https://portal.azure.com), selecteert u uw gebruikersnaam rechtsboven in de hoek, selecteert u het pictogram " **...** " voor meer opties en selecteert u **Mijn machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren.

Als u onderliggende resources wilt maken en beheren voor Data Factory, waaronder gegevenssets, gekoppelde services, pijplijnen, triggers en integratieruntimes, zijn de volgende vereisten van toepassing:

- Als u onderliggende resources in Azure Portal wilt maken en beheren, moet u de rol **Data Factory-inzender** op minimaal het niveau van de resourcegroep hebben.
- Voor het maken en beheren van onderliggende resources met PowerShell of de SDK is de rol **Inzender** op minimaal het resourceniveau voldoende.

Zie het artikel [Rollen toevoegen](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) voor voorbeelden van instructies voor het toevoegen van een gebruiker aan een rol.

Raadpleeg voor meer informatie de volgende artikelen:

- [Rol Data Factory-inzender](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Rollen en machtigingen voor Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-account

In deze Snelstart gaat u een algemeen Azure Storage-account (en dan met name voor Blob Storage) gebruiken als zowel *bron-* als *doel* gegevensarchieven. Raadpleeg het artikel [Een opslagaccount maken](../articles/storage/common/storage-account-create.md) als u geen Azure Storage-account hebt voor algemene doeleinden en er een wilt maken. 

#### <a name="get-the-storage-account-name"></a>De naam van het opslagaccount ophalen

In deze quickstart hebt u de naam van uw Azure Storage-account nodig. De volgende procedure bevat stappen waarmee u de naam van uw opslagaccount kunt ophalen: 

1. Ga in een webbrowser naar de [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-gebruikersnaam en-wachtwoord.
2. Selecteer in het menu Azure Portal **Alle services** en selecteer **Storage** > **Storage-accounts**. U kunt vanaf elke pagina ook *Storage-accounts* zoeken en selecteren.
3. Filter op de pagina **Storage-accounts** op uw opslagaccount (indien nodig) en selecteer vervolgens uw opslagaccount. 

U kunt vanaf elke pagina ook *Storage-accounts* zoeken en selecteren.

#### <a name="create-a-blob-container"></a>Een blobcontainer maken

In deze sectie maakt u in Azure Blob Storage een blobcontainer met de naam **adftutorial**.

1. Selecteer op de pagina Storage-account **Overzicht** > **Containers**.
2. Selecteer in de werkbalk van de pagina *\<Account name>*  - **Containers** **Container**.
3. Voer in het dialoogvenster **Nieuwe container** als naam **adftutorial** in en selecteer **OK**. De pagina *\<Account name>*  - **Containers** is bijgewerkt en bevat **adftutorial** in de lijst containers.

   :::image type="content" source="media/data-factory-quickstart-prerequisites/list-of-containers.png" alt-text="Lijst met containers":::


#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Voeg een invoermap en -bestand toe aan de blobcontainer

In dit gedeelte maakt u een map met de naam **invoer** in de container die u hebt gemaakt en vervolgens uploadt u een voorbeeldbestand naar de invoermap. Voor u begint, opent u een tekstverwerker zoals **Kladblok** en maakt u een bestand met de naam **emp.txt** met de volgende inhoud:

```emp.txt
John, Doe
Jane, Doe
```

Sla het bestand op in de map **c:\ADFv2QuickStartPSH**. (Maak de map als deze nog niet bestaat.) Ga terug naar de Azure-portal en voer de volgende stappen uit:

1. Selecteer op de pagina *\<Account name>*  - **Containers** waar u begon **adftutorial** uit de bijgewerkte lijst containers.

   1. Als u het venster hebt gesloten of naar een andere pagina bent gegaan, meldt u zich weer aan bij de [Azure Portal](https://portal.azure.com).
   1. Selecteer in het menu Azure Portal **Alle services** en selecteer **Storage** > **Storage-accounts**. U kunt vanaf elke pagina ook *Storage-accounts* zoeken en selecteren.
   1. Selecteer uw opslagaccount en selecteer vervolgens **Containers** > **adftutorial**.

2. Selecteer in de werkbalk van de containerpagina **adftutorial** **Uploaden**.
3. Selecteer op de pagina **Blob uploaden** het venster **Bestanden** en blader vervolgens naar en selecteer het bestand **emp.txt**.
4. Vouw de kop **Geavanceerd** uit. De pagina wordt nu als volgt weergegeven:

   :::image type="content" source="media/data-factory-quickstart-prerequisites/upload-blob-advanced.png" alt-text="De koppeling Geavanceerd selecteren":::

5. In het vak **Uploaden naar map** voert u **invoer** in.
6. Selecteer de knop **Uploaden**. Als het goed is, ziet u in de lijst nu het bestand **emp.txt**, evenals de uploadstatus hiervan.
7. Selecteer het pictogram **Sluiten** (**X**) om de pagina **Blob uploaden** te sluiten.

Houdt de containerpagina **adftutorial** open. U gaat hiermee aan het einde van deze QuickStart de uitvoer controleren.