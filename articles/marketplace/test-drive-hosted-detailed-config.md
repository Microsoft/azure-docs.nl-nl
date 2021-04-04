---
title: Gedetailleerde configuratie voor een gehoste test drive in azure Marketplace
description: Uitleg over configuratie stappen voor een gehoste test drive in de commerciële Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462945"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Gedetailleerde configuratie voor gehoste test stations

In dit artikel wordt beschreven hoe u een gehoste test drive configureert voor Dynamics 365 voor klant betrokkenheid of Dynamics 365 voor bewerkingen.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Configureren voor Dynamics 365-klant betrokkenheid

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/).
2. Als u geen toegang hebt tot de bovenstaande koppeling, moet u [hier](https://appsource.microsoft.com/partners/list-an-app) een aanvraag indienen om uw toepassing te publiceren. Zodra de aanvraag is gecontroleerd, krijgt u toegang om het publicatie proces te starten.
3. Zoek een bestaande aanbieding **voor dynamics 365 voor klanten** en maak een nieuwe aanbieding **voor Dynamics 365 voor klanten** .
4. Schakel het selectie vakje **een test drive inschakelen** in en selecteer een **type test drive** (zie onderstaande opsommings tekens) en selecteer vervolgens **Opslaan**.

    [![Schakel het selectie vakje een test drive inschakelen in.](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Type test drive** : Kies **micro soft hosted (Dynamics 365 voor Customer engagement & PowerApps)**. Dit geeft aan dat micro soft de service zal hosten en onderhouden die de test drive gebruikers inrichting en het ongedaan maken van de inrichting uitvoert.

5. Verleen Microsoft AppSource toestemming om test drive gebruikers in uw Tenant in te richten en deze ongedaan te maken met behulp van [deze instructies](./test-drive-azure-subscription-setup.md). In deze stap genereert u de **Azure AD-App-ID** en **Azure AD-App sleutel** waarden die hieronder worden genoemd.
6. Vul deze velden in op de pagina **technische configuratie van test station** .

    [![De pagina met de test drive technische configuratie.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maxi maal aantal gelijktijdige test stations** : de hoeveelheid gelijktijdige gebruikers die een actief test drive tegelijk kunnen uitvoeren. Elke gebruiker gebruikt een Dynamics-licentie terwijl hun test drive actief is. Zorg er dus voor dat u ten minste dit aantal gratis Dynamics-licenties voor test drive gebruikers hebt. 3 tot 5 wordt aanbevolen.
    - **Duur van test drive** : het aantal uur dat de test drive van de gebruiker actief is. Nadat de tijd is verstreken, wordt de toewijzing van de gebruiker ongedaan gemaakt met uw Tenant. We raden 2-24 uur aan, afhankelijk van de complexiteit van uw app. De gebruiker kan altijd een andere test drive aanvragen als deze niet langer actief zijn en de test drive opnieuw wilt openen.
    - **URL van exemplaar** : de URL waarnaar de test drive gebruiker wordt verzonden wanneer de test drive wordt gestart. Dit is doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app en voorbeeld gegevens zijn geïnstalleerd. Voorbeeld waarde: `https://testdrive.crm.dynamics.com` .
    - **URL Web API-exemplaar** : de Web-API-URL voor uw Dynamics 365-exemplaar. Als u deze waarde ophaalt, meldt u zich aan bij uw micro soft Dynamics 365-exemplaar en navigeert u naar  >  **aanpassings**  >  **ontwikkelaars resources**  >  **exemplaar Web-API** instellen en kopieert u het adres (URL). Voorbeeld waarde:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Een voor beeld van een web-API van een exemplaar.":::

    - **Rolnaam** : de naam van de aangepaste Dynamics 365-beveiligingsrol die u hebt gemaakt voor test drive of u kunt een bestaande rol gebruiken. Voor een nieuwe rol moeten mini maal vereiste bevoegdheden worden toegevoegd aan de rol om zich aan te melden bij een klant engagement-exemplaar. Raadpleeg de [minimale bevoegdheden die vereist zijn om u aan te melden bij micro soft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Dit is de rol die wordt toegewezen aan gebruikers tijdens hun test drive. Voorbeeld waarde: `testdriverole` .
    
        > [!IMPORTANT]
        > Zorg ervoor dat de controle van de beveiligings groep niet is toegevoegd. Hiermee kan de gebruiker worden gesynchroniseerd met het exemplaar van de klant engagement.

    - **Azure Active Directory Tenant-id** : de id van de Azure-Tenant voor uw Dynamics 365-exemplaar. Als u deze waarde wilt ophalen, meldt u zich aan bij Azure Portal en navigeert u naar **Azure Active Directory**  >  **Eigenschappen** en kopieert u de map-id. Voorbeeld waarde: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory Tenant naam** : de naam van de Azure-Tenant voor uw Dynamics 365-exemplaar. Gebruik de indeling `<tenantname>.onmicrosoft.com`. Voorbeeld waarde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory toepassings-id** : de id van de Azure Active Directory (AD)-app die u in stap 5 hebt gemaakt. Voorbeeld waarde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory client geheim** van de toepassing: geheim voor de Azure AD-app die u in stap 5 hebt gemaakt. Voorbeeld waarde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Geef de details van Marketplace-vermelding op. Selecteer **taal** om meer vereiste velden weer te geven.

    [![De pagina met details van Marketplace-lijst.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beschrijving** : een overzicht van uw test drive. Deze tekst wordt weer gegeven aan de gebruiker wanneer de test drive wordt ingericht. Dit veld ondersteunt HTML als u opgemaakte inhoud wilt leveren.
    - **Gebruikers handleiding** : een PDF-gebruikers handleiding die helpt test drive gebruikers inzicht te krijgen in het gebruik van uw app.
    - **Demo video testen** : een video met een demonstratie van uw app (optioneel).

## <a name="configure-for-dynamics-365-operations"></a>Configureren voor Dynamics 365-bewerkingen

2. Als u geen toegang hebt tot de bovenstaande koppeling, moet u [hier](https://appsource.microsoft.com/partners/list-an-app) een aanvraag indienen om uw toepassing te publiceren. Zodra de aanvraag is gecontroleerd, krijgt u toegang om het publicatie proces te starten.
3. Zoek een bestaande **dynamics 365 for Operations** -aanbieding of maak een nieuwe aanbieding **voor Dynamics 365 voor bewerkingen** .
4. Schakel het selectie vakje **een test drive inschakelen** in en selecteer een **type test drive** (zie onderstaande opsommings tekens) en selecteer vervolgens **Opslaan**.

    [![Schakel het selectie vakje een test drive inschakelen in.](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Type test drive** : Kies voor de optie **Dynamics 365 voor bewerkingen** . Dit betekent dat micro soft de service host en onderhoudt die de test drive gebruikers inrichting en het ongedaan maken van de inrichting uitvoert.

5. Verleen Microsoft AppSource toestemming om test drive gebruikers in uw Tenant in te richten en deze ongedaan te maken met behulp van [deze instructies](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md). In deze stap genereert u de **Azure AD-App-ID** en **Azure AD-App sleutel** waarden die hieronder worden genoemd.
6. Vul deze velden in op de pagina **technische configuratie van test station** .

    [![De pagina technische configuratie voor Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maxi maal aantal gelijktijdige test stations** : de hoeveelheid gelijktijdige gebruikers die een actief test drive tegelijk kunnen uitvoeren. Elke gebruiker gebruikt een Dynamics-licentie terwijl hun test drive actief is. Zorg er dus voor dat u ten minste dit aantal gratis Dynamics-licenties voor test drive gebruikers hebt. 3 tot 5 wordt aanbevolen.
    - **Duur van test drive** : het aantal uur dat de test drive van de gebruiker actief is. Nadat de tijd is verstreken, wordt de toewijzing van de gebruiker ongedaan gemaakt met uw Tenant. We raden 2-24 uur aan, afhankelijk van de complexiteit van uw app. De gebruiker kan altijd een andere test drive aanvragen als deze niet langer actief zijn en de test drive opnieuw wilt openen.
    - **URL van exemplaar** : de URL waarnaar de test drive gebruiker wordt verzonden wanneer de test drive wordt gestart. Dit is doorgaans de URL van uw Dynamics 365-exemplaar waarop uw app en voorbeeld gegevens zijn geïnstalleerd. Voorbeeld waarde: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory Tenant-id** : de id van de Azure-Tenant voor uw Dynamics 365-exemplaar. Als u deze waarde wilt ophalen, meldt u zich aan bij Azure Portal en navigeert u naar **Azure Active Directory**  >  **Eigenschappen** en kopieert u de map-id. Voorbeeld waarde: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory Tenant naam** : de naam van de Azure-Tenant voor uw Dynamics 365-exemplaar. Gebruik de indeling `<tenantname>.onmicrosoft.com`. Voorbeeld waarde: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory toepassings-id** : de id van de Azure Active Directory (AD)-app die u in stap 5 hebt gemaakt. Voorbeeld waarde: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory client geheim** van de toepassing: geheim voor de Azure AD-app die u in stap 5 hebt gemaakt. Voorbeeld waarde: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Rechts persoon van proef versie** : Geef een rechts persoon op om een proef gebruiker toe te wijzen. U kunt een nieuw item maken bij [het maken of wijzigen van een rechts persoon](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity).
    - **Rolnaam** : de AOT-naam (Toepassingsobjectstructuur) van de aangepaste Dynamics 365-beveiligingsrol die u hebt gemaakt voor test drive. Dit is de rol die wordt toegewezen aan gebruikers tijdens hun test drive.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="De pagina Beveiliging configuratie.":::

7. Geef de details van Marketplace-vermelding op. Selecteer **taal** om meer vereiste velden weer te geven.

    [![De pagina met details van Marketplace-lijst.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Beschrijving** : een overzicht van uw test drive. Deze tekst wordt weer gegeven aan de gebruiker wanneer de test drive wordt ingericht. Dit veld ondersteunt HTML als u opgemaakte inhoud wilt leveren.
    - **Gebruikers handleiding** : een PDF-gebruikers handleiding die helpt test drive gebruikers inzicht te krijgen in het gebruik van uw app.
    - **Demo video testen** : een video met een demonstratie van uw app (optioneel).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->