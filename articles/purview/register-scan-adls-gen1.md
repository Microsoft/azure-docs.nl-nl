---
title: Azure Data Lake Storage registreren en scannen (ADLS) gen1
description: In deze zelf studie wordt beschreven hoe u gegevens kunt scannen van Azure Data Lake Storage Gen1 naar Azure controle sfeer liggen.
author: kchandra
ms.author: kchandra
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: ee0b9238deb7805113f0cbfa28d0b60a114820a9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553224"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 registreren en scannen

In dit artikel wordt beschreven hoe u Azure Data Lake Storage Gen1 als gegevens bron registreert in azure controle sfeer liggen en hoe u er een scan op uitvoert.

> [!Note]
> Azure Data Lake Storage Gen2 is nu overal algemeen beschikbaar. We raden aan vandaag nog hierop over te stappen. Zie de [productpagina](https://azure.microsoft.com/services/storage/data-lake-storage/) voor meer informatie.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De Azure Data Lake Storage Gen1 gegevens bron ondersteunt de volgende functionaliteit:

- **Volledige en incrementele scans** voor het vastleggen van meta gegevens en classificatie in azure data Lake Storage gen1

- **Afkomst** tussen de gegevensassets voor ADF-Kopieer-en gegevensstroom activiteiten

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie instellen voor een scan

De volgende verificatie methoden worden ondersteund voor Azure Data Lake Storage Gen1:

- Beheerde identiteit
- Service-principal

### <a name="managed-identity-recommended"></a>Beheerde identiteit (aanbevolen)

Voor het gemak en de beveiliging kunt u controle sfeer liggen MSI gebruiken om te verifiëren bij uw gegevens opslag.

Als u een scan wilt instellen met behulp van het MSI-bestand van de Data Catalog, moet u eerst uw controle sfeer liggen-account de machtiging geven om de gegevens bron te scannen. Deze stap moet worden uitgevoerd *voordat* u de scan instelt, anders mislukt de scan.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>De Data Catalog MSI toevoegen aan een Azure Data Lake Storage Gen1-account

U kunt het MSI-bestand van de catalogus toevoegen aan het abonnement, de resource groep of het resource niveau, afhankelijk van waarvoor u de scan machtigingen wilt hebben.

> [!Note]
> U moet een eigenaar van het abonnement zijn om een beheerde identiteit toe te voegen aan een Azure-resource.

1. Zoek vanuit het [Azure Portal](https://portal.azure.com)het abonnement, de resource groep of de resource (bijvoorbeeld een Azure data Lake Storage gen1 Storage-account) dat u wilt toestaan dat de catalogus kan scannen.

2. Klik op **overzicht** en selecteer vervolgens **Data Explorer**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Toegangsbeheer kiezen":::

3. Klik op **toegang** in de bovenste navigatie balk

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Klik op toegang":::

4. Klik op **Toevoegen**. Voeg de **Catalogus controle sfeer liggen** toe aan de selectie gebruiker of groep selecteren. Selecteer de machtigingen **lezen** en **uitvoeren** . Zorg ervoor dat u **deze map en alle onderliggende items** selecteert in de optie toevoegen aan, zoals wordt weer gegeven in de onderstaande scherm afbeelding en klik op **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI-verificatie gegevens":::

5. Als uw sleutel kluis nog niet is verbonden met controle sfeer liggen, moet u [een nieuwe sleutel kluis verbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal voor het instellen van de scan
> [!Note]
> Nadat u het MSI-bestand van de catalogus hebt toegevoegd aan de gegevens bron, wacht u tot 15 minuten voordat u een scan hebt ingesteld.

Na ongeveer 15 minuten moet de catalogus de juiste machtigingen hebben om de resource (s) te kunnen scannen.

### <a name="service-principal"></a>Service-principal

Als u een Service-Principal wilt gebruiken, moet u eerst een van de volgende stappen maken:

1. Navigeer naar [Azure Portal](https://portal.azure.com).

2. Selecteer **Azure Active Directory** in het menu aan de linkerkant.

3. Selecteer **App-registraties**.

4. Selecteer **+ nieuwe toepassing registreren**.

5. Voer een naam in voor de **toepassing** (de Service Principal Name).

6. Selecteer **alleen accounts in deze organisatie Directory**.

7. Voor **omleidings-URI** selecteert u **Web** en voert u de gewenste URL in. het hoeft niet echt of werk te zijn.

8. Selecteer vervolgens **Registreren**.

9. Kopieer de waarden uit de weergave naam en de toepassings-ID.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>De service-principal van Data Catalog toevoegen aan een Azure Data Lake Storage Gen1-account
1. Zoek vanuit het [Azure Portal](https://portal.azure.com)het abonnement, de resource groep of de resource (bijvoorbeeld een Azure data Lake Storage gen1 Storage-account) dat u wilt toestaan dat de catalogus kan scannen.

2. Klik op **overzicht** en selecteer vervolgens **Data Explorer**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Toegangsbeheer kiezen":::

3. Klik op **toegang** in de bovenste navigatie balk

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Klik op toegang":::

4. Klik op **Toevoegen**. Voeg de **Service-Principal-toepassing** toe aan de selectie gebruiker of groep selecteren. Selecteer de machtigingen **lezen** en **uitvoeren** . Zorg ervoor dat u **deze map en alle onderliggende items** selecteert in de optie toevoegen aan, zoals wordt weer gegeven in de onderstaande scherm afbeelding en klik op **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="Service Principal-verificatie Details":::

5. Als uw sleutel kluis nog niet is verbonden met controle sfeer liggen, moet u [een nieuwe sleutel kluis verbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal voor het instellen van de scan.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Azure Data Lake Storage Gen1 gegevens bron registreren

Ga als volgt te werk om een nieuw ADLS Gen1-account in uw Data Catalog te registreren:

1. Navigeer naar uw controle sfeer liggen-Data Catalog.
2. Selecteer **bronnen** in het linkernavigatievenster.
3. Selecteer **Registreren**
4. Selecteer **Azure data Lake Storage gen1** bij **bron registreren**. 
5. Selecteer **Doorgaan**.

Ga als volgt te werk op het scherm bronnen registreren (Azure Data Lake Storage Gen1):

1. Voer een **naam** in die voor de gegevens bron wordt weer gegeven in de catalogus.
2. Kies uw abonnement om opslag accounts te filteren
3. Selecteer een opslagaccount
4. Een verzameling selecteren of een nieuwe maken (optioneel)
5. Volt ooien om de gegevens bron te registreren.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure controle sfeer liggen Data Catalog](how-to-browse-catalog.md)
- [Zoek in de Azure controle sfeer liggen-Data Catalog](how-to-search-catalog.md)
