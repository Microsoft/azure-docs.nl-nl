---
title: Verbeteringen in gebruikers beheer (preview)-Azure Active Directory | Microsoft Docs
description: Hierin wordt beschreven hoe Azure Active Directory zoeken, filteren en meer informatie over uw gebruikers door de gebruiker kan worden gezocht.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5038bde01a6b183a25a47f3b4e206c1ce80e6b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127835"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Verbeteringen in gebruikers beheer (preview) in Azure Active Directory

In dit artikel wordt beschreven hoe u de preview-versie van gebruikers beheer kunt gebruiken in de portal van Azure Active Directory (Azure AD). De pagina's **alle gebruikers** en **Verwijderde gebruikers** zijn bijgewerkt met meer informatie en kunnen gebruikers gemakkelijker vinden. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Wijzigingen in de preview zijn onder andere:

- Meer zicht bare gebruikers eigenschappen, waaronder de object-ID, de synchronisatie status van de Directory, het aanmaak type en de identiteits verlener
- Als u nu zoeken, kunt u zoeken in subtekenreeksen en gecombineerde Zoek opdrachten van namen, e-mails en object-Id's
- Uitgebreid filteren op gebruikers type (lid, gast, geen), synchronisatie status van de Directory, aanmaak type, bedrijfs naam en domein naam
- Nieuwe sorteer mogelijkheden op Eigenschappen als naam en user principal name
- Een nieuw totaal aantal gebruikers dat updates met Zoek opdrachten of filters heeft

> [!NOTE]
> Deze preview is momenteel niet beschikbaar voor Azure AD B2C-tenants.

## <a name="find-the-preview"></a>De preview-versie zoeken

De preview-versie is standaard ingeschakeld, zodat u deze meteen kunt gebruiken. U kunt de nieuwste functies en verbeteringen bekijken door preview- **functies** te selecteren op de pagina **alle gebruikers** . Op alle pagina's die zijn bijgewerkt als onderdeel van deze preview, wordt een voorbeeld code weer gegeven. Als u problemen ondervindt, kunt u teruggaan naar de oude ervaring:

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) en selecteer **gebruikers**.
1. Selecteer op de pagina **gebruikers – alle gebruikers** de banner boven aan de pagina.
1. In het deel venster **Preview-functies** schakelt u **uitgebreid gebruikers beheer** uit.

   ![Hoe en wanneer u uitgebreid gebruikers beheer in-en uitschakelt](./media/users-search-enhanced/enable-preview.png)

We stellen uw feedback op prijs zodat we onze ervaring kunnen verbeteren.

## <a name="more-user-properties"></a>Meer gebruikers eigenschappen

Er zijn enkele wijzigingen aangebracht in de beschik bare kolommen op de pagina's **alle gebruikers** en **Verwijderde gebruikers** . Naast de bestaande kolommen die we bieden voor het beheren van uw lijst met gebruikers, hebben we nog enkele kolommen toegevoegd.

### <a name="all-users-page"></a>Pagina alle gebruikers

Hieronder ziet u de eigenschappen van de gebruiker op de pagina **alle gebruikers** :

- Naam: de weergave naam van de gebruiker.
- Principal-naam van gebruiker: de user principal name (UPN) van de gebruiker.
- Gebruikers type: lid, gast, geen.
- Aanmaak tijd: de datum en tijd waarop de gebruiker is gemaakt.
- Functie: de functie van de gebruiker.
- Afdeling: de afdeling waarmee de gebruiker werkt.
- Directory-synchronisatie: geeft aan of de gebruiker is gesynchroniseerd vanuit een on-premises Directory.
- Identiteits verlener: de verleners van de identiteit die wordt gebruikt om zich aan te melden bij een gebruikers account.
- Object-ID: de object-ID van de gebruiker.
- Type maken: Hiermee wordt aangegeven hoe het gebruikers account is gemaakt.
- Bedrijfs naam: de bedrijfs naam waaraan de gebruiker is gekoppeld.
- Uitnodigings status: de status van de uitnodiging voor een gast gebruiker.
- Mail: het e-mail adres van de gebruiker.
- Laatste aanmelding: de datum waarop de gebruiker zich voor het laatst heeft aangemeld. Deze eigenschap is alleen zichtbaar voor gebruikers met machtigingen voor het lezen van controle Logboeken (Reporting_ApplicationAuditLogs_Read)

![nieuwe gebruikers eigenschappen die worden weer gegeven op de pagina's van alle gebruikers en verwijderde gebruikers](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Pagina verwijderde gebruikers

De pagina **Verwijderde gebruikers** bevat alle kolommen die beschikbaar zijn op de pagina **alle gebruikers** en enkele extra kolommen, namelijk:

- Verwijderings datum: de datum waarop de gebruiker voor het eerst is verwijderd uit de organisatie (de gebruiker is herstorbaar).
- Permanente verwijderings datum: de datum waarna het proces van het permanent verwijderen van de gebruiker uit de organisatie automatisch wordt gestart.
- Origineel user principal name: de oorspronkelijke UPN van de gebruiker voordat de object-ID is toegevoegd als voor voegsel voor de verwijderde UPN.

> [!NOTE]
> Verwijderings datums worden weer gegeven in Coordinated Universal Time (UTC).

Sommige kolommen worden standaard weer gegeven. Als u andere kolommen wilt toevoegen, selecteert u **kolommen** op de pagina, selecteert u de kolom namen die u wilt toevoegen en selecteert u **OK** om uw voor keuren op te slaan.

### <a name="identity-issuers"></a>Identiteits verleners

Selecteer een vermelding in de kolom **identiteits verlener** voor een wille keurige gebruiker om aanvullende informatie over de certificaat verlener te bekijken, inclusief het aanmeldings type en de toegewezen id van de verlener. De vermeldingen in de kolom **identiteits verlener** kunnen meerdere waarden hebben. Als er meerdere verleners van de identiteit van de gebruiker zijn, wordt het woord meerdere weer geven in de kolom **identiteits verlener** op **alle gebruikers** en **Verwijderde gebruikers** pagina's, en in het detail venster worden alle verleners weer geven.

> [!NOTE]
> De **bron** kolom wordt vervangen door meerdere kolommen, inclusief het **aanmaak type**, de **Directory synchronisatie** en de **identiteits verlener** voor gedetailleerdere filters.

## <a name="user-list-search"></a>Zoek opdracht gebruikers lijst

Wanneer u een zoek reeks invoert, gebruikt de zoek actie nu ' begint met ' en wordt met subtekenreeks zoeken namen, e-mail berichten of object-Id's in één zoek opdracht gevonden. U kunt een van deze kenmerken in het zoekvak invoeren en de zoek opdracht zoekt automatisch naar de volledige eigenschappen om eventuele overeenkomende resultaten te retour neren. De subtekenreeks zoeken wordt alleen uitgevoerd op hele woorden. U kunt dezelfde zoek opdracht uitvoeren op de pagina's **alle gebruikers** en **Verwijderde gebruikers** .

## <a name="user-list-filtering"></a>Filteren van gebruikers lijst

Filter mogelijkheden zijn uitgebreid om meer filter opties te bieden voor de pagina's **alle gebruikers** en **Verwijderde gebruikers** . U kunt nu tegelijkertijd filteren op meerdere eigenschappen, en u kunt filteren op meer eigenschappen.

### <a name="filtering-all-users-list"></a>De lijst alle gebruikers filteren

Hieronder ziet u de eigenschappen die kunnen worden gefilterd op de pagina **alle gebruikers** :

- Gebruikers type: lid, gast, geen
- Status Directory gesynchroniseerd: Ja, nee
- Type maken: uitnodiging, E-mail geverifieerd, lokaal account
- Aanmaak tijd: afgelopen 7, 14, 30, 90, 360 of >360 dagen geleden
- Functie: Voer een taak titel in
- Afdeling: Voer een afdelings naam in
- Groep: zoeken naar een groep
- Uitnodigings status: acceptatie in behandeling, geaccepteerd
- Domein naam: Voer een domein naam in
- Bedrijfs naam: Voer een bedrijfs naam in
- Administratieve eenheid: Selecteer deze optie om het bereik van de gebruikers die u bekijkt tot één beheer eenheid te beperken. Zie beheer van [administratieve eenheden](../roles/administrative-units.md)voor meer informatie.

### <a name="filtering-deleted-users-list"></a>Lijst met verwijderde gebruikers filteren

De pagina **Verwijderde gebruikers** bevat extra filters, niet op de pagina **alle gebruikers** . Hieronder ziet u de eigenschappen die kunnen worden gefilterd op de pagina **Verwijderde gebruikers** :

- Gebruikers type: lid, gast, geen
- Status Directory gesynchroniseerd: Ja, nee
- Type maken: uitnodiging, E-mail geverifieerd, lokaal account
- Aanmaak tijd: afgelopen 7, 14, 30, 90, 360 of > 360 dagen geleden
- Functie: Voer een taak titel in
- Afdeling: Voer een afdelings naam in
- Uitnodigings status: acceptatie in behandeling, geaccepteerd
- Verwijderings datum: afgelopen 7, 14 of 30 dagen
- Domein naam: Voer een domein naam in
- Bedrijfs naam: Voer een bedrijfs naam in
- Datum van definitieve verwijdering: laatste 7, 14 of 30 dagen

## <a name="user-list-sorting"></a>Sortering gebruikers lijst

U kunt nu sorteren op naam en user principal name op de pagina's **alle gebruikers** en **Verwijderde gebruikers** . U kunt in de lijst **Verwijderde gebruikers** ook sorteren op verwijderings datum.

## <a name="user-list-counts"></a>Aantal gebruikers lijsten

U kunt het totale aantal gebruikers weer geven op de pagina's **alle gebruikers** en **Verwijderde gebruikers** . Wanneer u de lijsten zoekt of filtert, wordt de telling bijgewerkt om het totale aantal gevonden gebruikers weer te geven.

![Afbeelding van aantallen gebruikers lijsten op de pagina alle gebruikers](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Vraag | Antwoord
-------- | ------
Waarom wordt de verwijderde gebruiker nog steeds weer gegeven wanneer de permanente verwijderings datum is verstreken? | De permanente verwijderings datum wordt weer gegeven in de UTC-tijd zone. Dit komt dus mogelijk niet overeen met de huidige tijd zone. Deze datum is ook de vroegste datum waarna de gebruiker permanent wordt verwijderd uit de organisatie, zodat deze nog steeds kan worden verwerkt. Gebruikers die permanent zijn verwijderd, worden automatisch uit de lijst verwijderd.
Wat gebeurt er met de bulk mogelijkheden voor gebruikers en gasten? | De bulk bewerkingen zijn allemaal nog steeds beschikbaar voor gebruikers en gasten, waaronder bulksgewijs maken, bulksgewijs uitnodigen, bulksgewijs verwijderen en gebruikers downloaden. We hebben deze zojuist samengevoegd in een menu met de naam **bulk bewerkingen**. U kunt de opties voor **bulk bewerkingen** vinden boven aan de pagina **alle gebruikers** .
Wat is er gebeurd met de bron kolom? | De **bron** kolom is vervangen door andere kolommen die vergelijk bare informatie geven, terwijl u kunt filteren op die waarden onafhankelijk van elkaar. Voor beelden zijn onder meer het **type maken**, de **Directory is gesynchroniseerd** en de identiteit van de **Uitgever**.
Wat is er gebeurd met de kolom gebruikers naam? | De kolom **gebruikers naam** is nog steeds, maar is gewijzigd in Principal-naam van **gebruiker**. Dit komt overeen met de informatie in die kolom. U ziet ook dat de volledige Principal-naam van de gebruiker nu wordt weer gegeven voor B2B-gasten. Dit komt overeen met wat u in MS Graph zou krijgen.  

## <a name="next-steps"></a>Volgende stappen

Gebruikers bewerkingen

- [Profiel gegevens toevoegen of wijzigen](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Gebruikers toevoegen of verwijderen](../fundamentals/add-users-azure-active-directory.md)

Bulk bewerkingen

- [Lijst met gebruikers downloaden](users-bulk-download.md)
- [Gebruikers bulksgewijs toevoegen](users-bulk-add.md)
- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Gebruikers bulksgewijs herstellen](users-bulk-restore.md)
