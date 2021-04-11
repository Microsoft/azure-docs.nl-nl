---
title: 'Quickstart: Een Azure Data Catalog maken'
description: In deze snelstart wordt beschreven hoe u een Azure Data Catalog maakt met behulp van de Azure-portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a56e5a4cae7c5a8e931b074f08a7152e53a8eb31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674730"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Quickstart: Een Azure Data Catalog maken

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Azure Data Catalog is een volledig beheerde cloudservice die als een registratie- en detectiesysteem voor gegevensassets van ondernemingen fungeert. Zie [Wat is Azure Data Catalog?](overview.md) voor een gedetailleerd overzicht.

Deze snelstart helpt u om aan de slag te gaan met het maken van een Azure Data Catalog.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

> [!Note]
> Vanwege Azure-beveiligingsvereisten dwingt Azure Data Catalog TLS 1.2 (Transport Layer Security) af. TLS 1.0 en TLS 1.1 zijn uitgeschakeld. Er kunnen fouten optreden bij het uitvoeren van het hulpprogramma voor registratie als uw computer niet is bijgewerkt naar TLS 1.2. Zie [Transport Layer Security (1.2) inschakelen](/mem/configmgr/core/plan-design/security/enable-tls-1-2) om uw computer bij te werken naar TLS 1.2.

Om aan de slag te gaan, hebt u het volgende nodig:

* Een [Microsoft Azure](https://azure.microsoft.com/)-abonnement.
* U moet uw eigen [Azure Active Directory-tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) hebben.

Voor het instellen van Data Catalog moet u eigenaar of mede-eigenaar van een Azure-abonnement zijn.

## <a name="create-a-data-catalog"></a>Een gegevenscatalogus maken

U kunt per organisatie (Azure Active Directory-domein) slechts één gegevenscatalogus inrichten. Als de eigenaar of mede-eigenaar van het Azure-abonnement waartoe dit Azure Active Directory-domein behoort, al een catalogus heeft gemaakt, kunt u daarom geen nieuwe catalogus maken, zelfs niet als u meerdere Azure-abonnementen hebt. Als u wilt testen of er al een gegevenscatalogus is gemaakt door een gebruiker in uw Azure Active Directory-domein, gaat u naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en controleert u of u de catalogus ziet. Als er al een catalogus voor u is gemaakt, slaat u de volgende procedure over en gaat u naar de volgende sectie.

1. Ga naar de [Azure-portal](https://portal.azure.com) > **Een resource maken** en selecteer **Data Catalog**.

    ![Knop Azure Data Catalog maken](media/data-catalog-get-started/data-catalog-create.png)

2. Geef een **naam** op voor de gegevenscatalogus, het **abonnement** dat u wilt gebruiken, de **locatie** voor de catalogus en de **prijscategorie**. Selecteer vervolgens **Maken**.

3. Ga naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en klik op **Gegevens publiceren**.

   ![Azure Data Catalog - de knop Gegevens publiceren](media/data-catalog-get-started/data-catalog-publish-data.png)

   U kunt ook naar de Data Catalog-startpagina gaan via de pagina [Data Catalog-service](https://azure.microsoft.com/services/data-catalog) door **Aan de slag** te selecteren.

   ![Azure Data Catalog - marketingstartpagina](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Ga naar de pagina **Instellingen**.

    ![Azure Data Catalog - gegevenscatalogus inrichten](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Vouw **Prijzen** uit en verifieer uw Azure Data Catalog-**editie** (gratis of Standard).

    ![Azure Data Catalog - editie selecteren](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Als u de *Standard*-editie als prijscategorie kiest, kunt u **Beveiligingsgroepen** uitvouwen en Active Directory-beveiligingsgroepen toegang tot Data Catalog toestaan en automatische aanpassing van facturering inschakelen.

    ![Azure Data Catalog-beveiligingsgroepen](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Vouw **Catalogusgebruikers** uit en klik op **Toevoegen** om gebruikers toe te voegen aan de gegevenscatalogus. U wordt automatisch toegevoegd aan deze groep.

    ![Azure Data Catalog - gebruikers](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Als u de *Standard*-editie als prijscategorie hebt gekozen, kunt u **Woordenlijstbeheerders** uitvouwen en op **Toevoegen** klikken om woordenlijstbeheerders toe te voegen. U wordt automatisch toegevoegd aan deze groep.

    ![Azure Data Catalog-woordenlijstbeheerders](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Vouw **Catalogusbeheerders** uit en klik op **Toevoegen** om meer beheerders toe te voegen aan de gegevenscatalogus. U wordt automatisch toegevoegd aan deze groep.

    ![Azure Data Catalog - beheerders](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Vouw **Portaltitel** uit en voeg extra tekst toe die wordt weergegeven in de titel van de portal.

    ![Azure Data Catalog - Portaltitel](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Nadat u de pagina **Instellingen** hebt ingevuld, gaat u naar de pagina **Publiceren**.

    ![Azure Data Catalog - gemaakt](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Zoeken naar een gegevenscatalogus in de Azure Portal

1. Ga op een afzonderlijk tabblad in de webbrowser of in een afzonderlijke geopend browservenster naar de [Azure Portal](https://portal.azure.com) en meld u aan met hetzelfde account dat u in de vorige stap hebt gebruikt voor het maken van de gegevenscatalogus.

2. Selecteer **Alle services** en klik vervolgens op **Data Catalog**.

    ![Azure Data Catalog - bladeren door Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    U ziet de gegevenscatalogus die u hebt gemaakt.

    ![Azure Data Catalog - catalogus in lijst bekijken](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klik op de catalogus die u hebt gemaakt. U ziet de blade **Gegevenscatalogus** in de portal.

   ![Azure Data Catalog - blade in portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. U kunt de eigenschappen van de gegevenscatalogus bekijken en bijwerken. Klik bijvoorbeeld op **Prijscategorie** en wijzig de editie.

    ![Azure Data Catalog - prijscategorie](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u een Azure Data Catalog maakt voor uw organisatie. U kunt nu gegevensbronnen registreren in uw Data Catalog.

> [!div class="nextstepaction"]
> [Gegevensbronnen registreren in Azure Data Catalog](data-catalog-how-to-register.md)