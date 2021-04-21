---
title: Gedetailleerde configuratie voor een gehoste test drive in Azure Marketplace
description: Uitleg over configuratiestappen voor een gehoste test drive in de commerciële marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 04/20/2021
ms.openlocfilehash: f11f1d5601a61bbd9b8729b478c278db8d3e73dc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812408"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Gedetailleerde configuratie voor gehoste teststations

In dit artikel wordt beschreven hoe u een gehoste test drive voor Dynamics 365 for Customer Engagement of Dynamics 365 for Operations configureert.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Configureren voor Dynamics 365 Customer Engagement

1. Meld u aan [bij Partner Center](https://partner.microsoft.com/).
2. Als u geen toegang hebt tot de bovenstaande koppeling, moet u hier een aanvraag [indienen om](https://appsource.microsoft.com/partners/list-an-app) uw toepassing te publiceren. Zodra we de aanvraag hebben beoordeeld, krijgt u toegang om het publicatieproces te starten.
3. Zoek een bestaande **Dynamics 365 for Customer Engagement-aanbieding** of maak een nieuwe **Dynamics 365 for Customer Engagement-aanbieding.**
4. Schakel het **selectievakje Een test drive** in en selecteer een **type** test drive (zie het onderstaande opsommingsteken) en selecteer **vervolgens Opslaan.**

    [![Schakel het selectievakje Een test drive in.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Type test drive:** kies Gehost door **Microsoft (Dynamics 365 for Customer Engagement & PowerApps)**. Dit geeft aan dat Microsoft de service host en onderhoudt die de test drive het inrichten en de inrichting van gebruikers inrichten.

5. Verleen Microsoft AppSource machtiging voor het inrichten en de inrichting van test drive gebruikers in uw tenant met behulp [van deze instructies.](./test-drive-azure-subscription-setup.md) In deze stap genereert u de waarden Azure AD-app **id** en **Azure AD-app sleutel die** hieronder worden vermeld.
6. Vul deze velden in op de **pagina Technische configuratie test** drive.

    [![De pagina test drive technische configuratie.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximaal aantal gelijktijdige** teststations: het aantal gelijktijdige gebruikers dat een actieve test drive tegelijkertijd kan uitvoeren. Elke gebruiker gebruikt een Dynamics-licentie terwijl de test drive actief is. Zorg er dus voor dat u ten minste zoveel Dynamics-licenties beschikbaar hebt voor test drive gebruikers. We raden 3 tot 5 aan.
    - **Duur van teststation:** het aantal uren dat de test drive van de gebruiker actief is. Nadat de tijd is verstreken, wordt de aanprovisioning van de gebruiker in uw tenant verwijderd. We raden u 2-24 uur aan, afhankelijk van de complexiteit van uw app. De gebruiker kan altijd een andere test drive als hij geen tijd meer heeft en weer toegang wil tot test drive toegang.
    - **Exemplaar-URL:** de URL test drive naar de gebruiker wordt verzonden wanneer deze de test drive. Dit is doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app en voorbeeldgegevens zijn geïnstalleerd. Voorbeeldwaarde: `https://testdrive.crm.dynamics.com` .
    - **Url van web-API voor exemplaar:** de web-API-URL voor uw Dynamics 365-exemplaar. Haal deze waarde op door u aan te melden bij uw Microsoft Dynamics 365-exemplaar en te navigeren naar **Setting** Customization Developer Resources Instance Web API en het adres  >    >    >   (URL) te kopiëren. Voorbeeldwaarde:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Een voorbeeld van de web-API van het exemplaar.":::

    - **Rolnaam:** de naam van de aangepaste Dynamics 365-beveiligingsrol die u hebt gemaakt voor test drive of u kunt een bestaande rol gebruiken. Een nieuwe rol moet minimaal vereiste bevoegdheden aan de rol hebben toegevoegd om zich aan te melden bij een Customer Engagement-exemplaar. Raadpleeg [Minimale bevoegdheden die zijn vereist om u aan te melden bij Microsoft Dynamics 365.](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365) Dit is de rol die wordt toegewezen aan gebruikers tijdens hun test drive. Voorbeeldwaarde: `testdriverole` .
    
        > [!IMPORTANT]
        > Zorg ervoor dat de beveiligingsgroepscontrole niet is toegevoegd. Hierdoor kan de gebruiker worden gesynchroniseerd met het Customer Engagement-exemplaar.

    - **Azure Active Directory tenant-id:** de id van de Azure-tenant voor uw Dynamics 365-exemplaar. Als u deze waarde wilt ophalen, meld u zich aan bij Azure Portal en navigeert u naar **Azure Active Directory**  >  **Eigenschappen en** kopieert u de map-id. Voorbeeldwaarde: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory tenantnaam:** de naam van de Azure-tenant voor uw Dynamics 365-exemplaar. Gebruik de indeling `<tenantname>.onmicrosoft.com`. Voorbeeldwaarde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory toepassings-id:** de id van de Azure Active Directory-app (AD) die u in stap 5 hebt gemaakt. Voorbeeldwaarde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory toepassingsclientgeheim :** geheim voor de Azure AD-app die is gemaakt in stap 5. Voorbeeldwaarde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Geef de details van de Marketplace-vermelding op. Selecteer **Taal om** de vereiste velden te bekijken.

    [![De pagina Met details van Marketplace-vermelding.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beschrijving:** een overzicht van uw test drive. Deze tekst wordt weergegeven aan de gebruiker terwijl de test drive wordt ingericht. Dit veld ondersteunt HTML als u opgemaakte inhoud wilt bieden.
    - **Gebruikershandleiding:** een PDF-gebruikershandleiding waarmee u test drive begrijpen hoe u uw app gebruikt.
    - **Demovideo voor test drive:** een video waarin uw app wordt getoond (optioneel).

## <a name="configure-for-dynamics-365-operations"></a>Configureren voor Dynamics 365-bewerkingen

2. Als u geen toegang hebt tot de bovenstaande koppeling, moet u hier een aanvraag [indienen om](https://appsource.microsoft.com/partners/list-an-app) uw toepassing te publiceren. Zodra we de aanvraag hebben beoordeeld, krijgt u toegang om het publicatieproces te starten.
3. Zoek een bestaande **Dynamics 365 for Operations-aanbieding** of maak een nieuwe **Dynamics 365 for Operations-aanbieding.**
4. Schakel het **selectievakje Een test drive** in en selecteer een **type** test drive (zie het onderstaande opsommingsteken) en selecteer **vervolgens Opslaan.**

    [![Schakel het selectievakje Een test drive in.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Type test drive:** kies **de optie Dynamics 365 for Operations.** Dit betekent dat Microsoft de service host en onderhoudt die de test drive het inrichten en de inrichting van gebruikers inrichten.

5. Verleen Microsoft AppSource machtiging voor het inrichten en de inrichting van test drive gebruikers in uw tenant met behulp [van deze instructies.](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) In deze stap genereert u de waarden **Azure AD-app id en** Azure AD-app sleutel **die** hieronder worden vermeld.
6. Vul deze velden in op de **pagina Technische configuratie test** drive.

    [![De pagina voor technische configuratie van Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maximum aantal gelijktijdige** teststations: het aantal gelijktijdige gebruikers dat een actieve test drive tegelijkertijd kan uitvoeren. Elke gebruiker gebruikt een Dynamics-licentie terwijl de test drive actief is. Zorg er dus voor dat u ten minste zoveel Dynamics-licenties beschikbaar hebt voor test drive gebruikers. We raden 3 tot en met 5 aan.
    - **Duur van teststation:** het aantal uren dat de test drive van de gebruiker actief is. Nadat de tijd is verstreken, wordt de aanprovisioning van de gebruiker in uw tenant verwijderd. We raden u 2-24 uur aan, afhankelijk van de complexiteit van uw app. De gebruiker kan altijd een andere test drive als hij geen tijd meer heeft en weer toegang tot de test drive wil.
    - **Exemplaar-URL:** de URL test drive naar de gebruiker wordt verzonden wanneer deze de test drive. Dit is doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app en voorbeeldgegevens zijn geïnstalleerd. Voorbeeldwaarde: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory tenant-id:** de id van de Azure-tenant voor uw Dynamics 365-exemplaar. Als u deze waarde wilt ophalen, meld u zich aan bij Azure Portal en navigeert u naar **Azure Active Directory**  >  **Eigenschappen en** kopieert u de map-id. Voorbeeldwaarde: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory tenantnaam:** de naam van de Azure-tenant voor uw Dynamics 365-exemplaar. Gebruik de indeling `<tenantname>.onmicrosoft.com`. Voorbeeldwaarde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory toepassings-id:** de id van de Azure Active Directory-app (AD) die u in stap 5 hebt gemaakt. Voorbeeldwaarde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory toepassingsclientgeheim :** geheim voor de Azure AD-app die is gemaakt in stap 5. Voorbeeldwaarde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Juridische entiteit voor proefversie:** geef een juridische entiteit op om een proefgebruiker toe te wijzen. U kunt een nieuwe maken via Een juridische [entiteit maken of wijzigen.](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)
    - **Rolnaam:** de AOT-naam (structuur van toepassingsobject) van de aangepaste Dynamics 365-beveiligingsrol die u hebt gemaakt voor test drive. Dit is de rol die wordt toegewezen aan gebruikers tijdens hun test drive.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="De pagina beveiligingsconfiguratie.":::

7. Geef de details van de Marketplace-vermelding op. Selecteer **Taal om** de vereiste velden te bekijken.

    [![De pagina Met details van Marketplace-vermelding.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beschrijving:** een overzicht van uw test drive. Deze tekst wordt weergegeven aan de gebruiker terwijl de test drive wordt ingericht. Dit veld ondersteunt HTML als u opgemaakte inhoud wilt bieden.
    - **Gebruikershandleiding:** een PDF-gebruikershandleiding waarmee u test drive begrijpen hoe u uw app gebruikt.
    - **Demovideo voor test drive:** een video waarin uw app wordt getoond (optioneel).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->