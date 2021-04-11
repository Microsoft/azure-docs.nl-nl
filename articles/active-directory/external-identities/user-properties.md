---
title: Eigenschappen van een B2B-gast gebruiker-Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B gast gebruikers eigenschappen en-statussen vóór en na inwisseling van de uitnodiging
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/18/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08ed02c65db201e18ce710633b421edb5ecb2db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655696"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Eigenschappen van een Azure Active Directory B2B-samenwerkings gebruiker

In dit artikel worden de eigenschappen en statussen van het B2B-gast gebruikers object in Azure Active Directory (Azure AD) voor en na de inwisseling van de uitnodiging beschreven. Een Azure AD Business-to-Business-samenwerkings gebruiker (B2B) is een gebruiker met User type = Guest. Deze gast gebruiker is doorgaans van een partner organisatie en heeft beperkte bevoegdheden in de uitgenodigde map.

Afhankelijk van de behoeften van de uitgenodigde organisatie, kan een Azure AD B2B-samenwerkings gebruiker een van de volgende account statussen hebben:

- Status 1: in een extern exemplaar van Azure AD en wordt weer gegeven als gast gebruiker in de uitnodigende organisatie. In dit geval meldt de B2B-gebruiker zich aan met behulp van een Azure AD-account dat deel uitmaakt van de uitgenodigde Tenant. Als de partner organisatie geen gebruik maakt van Azure AD, wordt de gast gebruiker in azure AD nog steeds gemaakt. De vereisten zijn dat ze hun uitnodiging inwisselen en Azure AD verifieert hun e-mail adres. Deze indeling wordt ook wel een just-in-time (JIT) pacht of een ' virale ' pacht genoemd.

   > [!IMPORTANT]
   > **Vanaf 2021 oktober** heeft micro soft geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde Azure AD-accounts en-tenants voor B2B-samenwerkings scenario's. In de voorbereiding raden wij klanten aan om te kiezen voor de [verificatie van de eenmalige wachtwoordcode e-mailen](one-time-passcode.md). We waarderen uw feedback over deze openbare preview-functie en willen graag nog meer manieren te maken om samen te werken.

- Status 2: in een micro soft-account en wordt weer gegeven als gast gebruiker in de organisatie van de host. In dit geval meldt de gast gebruiker zich aan met een Microsoft-account of een sociaal account (google.com of vergelijkbaar). De identiteit van de uitgenodigde gebruiker wordt gemaakt als een Microsoft-account in de directory van de organisatie die wordt uitgenodigd tijdens de aanbieding.

- Status 3: in het on-premises Active Directory van de host-organisatie en gesynchroniseerd met de Azure AD van de host-organisatie. U kunt Azure AD Connect gebruiken om de partner accounts te synchroniseren met de Cloud als Azure AD B2B-gebruikers met User type = gast. Zie [lokaal beheerde partner accounts toegang verlenen tot cloud resources](hybrid-on-premises-to-cloud.md).

- Status 4: in de Azure AD van de host-organisatie met User type = gast en referenties die de host-organisatie beheert.

  ![Diagram waarin de vier gebruikers Staten worden weer gegeven](media/user-properties/redemption-diagram.png)


Nu gaan we kijken hoe een Azure AD B2B-samenwerkings gebruiker eruitziet in azure AD.

### <a name="before-invitation-redemption"></a>Vóór inwisseling van uitnodiging

Status 1-en status 2-accounts zijn het resultaat van het uitnodigen van gast gebruikers om samen te werken met de eigen referenties van de gast gebruikers. Wanneer de uitnodiging voor het eerst naar de gast gebruiker wordt verzonden, wordt er een account in uw directory gemaakt. Aan dit account zijn geen referenties gekoppeld, omdat de verificatie wordt uitgevoerd door de ID-provider van de gast gebruiker. De **bron** eigenschap voor het gast gebruikers account in uw directory is ingesteld op **uitgenodigde gebruiker**. 

![Scherm opname van de gebruikers eigenschappen voordat de aanbieding wordt weer gegeven](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Na inwisseling van uitnodiging

Nadat de gast gebruiker de uitnodiging heeft geaccepteerd, wordt de eigenschap **Source** bijgewerkt op basis van de ID-provider van de gast gebruiker.

Voor gast gebruikers in status 1 is de **bron** **externe Azure Active Directory**.

![Gast gebruiker status 1 na aflossing aanbieding](media/user-properties/after-redemption-state1.png)

Voor gast gebruikers met de status 2 is de **bron** een **micro soft-account**.

![Status 2 gast gebruiker na inwisseling van aanbieding](media/user-properties/after-redemption-state2.png)

Voor gast gebruikers met de status 3 en status 4 is de eigenschap **Source** ingesteld op **Azure Active Directory** of **Windows Server Active Directory**, zoals wordt beschreven in de volgende sectie.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Belangrijkste eigenschappen van de Azure AD B2B-samenwerkings gebruiker
### <a name="usertype"></a>UserType
Met deze eigenschap wordt de relatie van de gebruiker met de pacht van de host aangegeven. Deze eigenschap kan twee waarden hebben:
- Lid: deze waarde geeft een werk nemer van de host-organisatie aan en een gebruiker in de salaris administratie van de organisatie. Deze gebruiker verwacht bijvoorbeeld dat deze toegang moet hebben tot alleen-interne sites. Deze gebruiker wordt niet beschouwd als een externe samen werker.

- Gast: deze waarde geeft aan dat een gebruiker die niet als intern wordt beschouwd bij het bedrijf, zoals een externe mede werker, partner of klant. Een dergelijke gebruiker wordt niet verwacht dat er een interne memo van een CEO wordt ontvangen of voor delen van het bedrijf worden ontvangen.

  > [!NOTE]
  > De User type heeft geen relatie met de manier waarop de gebruiker zich aanmeldt, de Directory-rol van de gebruiker, enzovoort. Met deze eigenschap wordt de relatie van de gebruiker met de organisatie van de host aangegeven en kan de organisatie beleid afdwingen dat afhankelijk is van deze eigenschap.

Voor details over prijzen raadpleegt u [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory).

### <a name="source"></a>Bron
Deze eigenschap geeft aan hoe de gebruiker zich aanmeldt.

- Uitgenodigde gebruiker: deze gebruiker is uitgenodigd, maar heeft nog geen uitnodiging ingewisseld.

- Externe Azure Active Directory: deze gebruiker bevindt zich in een externe organisatie en verifieert met behulp van een Azure AD-account dat deel uitmaakt van de andere organisatie. Dit type aanmelding komt overeen met status 1.

- Microsoft-account: deze gebruiker bevindt zich in een Microsoft-account en verifieert met behulp van een Microsoft-account. Dit type aanmelding komt overeen met status 2.

- Windows Server Active Directory: deze gebruiker is aangemeld vanaf de on-premises Active Directory die deel uitmaakt van deze organisatie. Dit type aanmelding komt overeen met status 3.

- Azure Active Directory: deze gebruiker wordt geverifieerd met behulp van een Azure AD-account dat deel uitmaakt van deze organisatie. Dit type aanmelding komt overeen met staat 4.
  > [!NOTE]
  > Bron-en User type zijn onafhankelijke eigenschappen. Een waarde van de bron impliceert niet een bepaalde waarde voor User type.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Kunnen Azure AD B2B-gebruikers worden toegevoegd als leden in plaats van gasten?
Normaal gesp roken zijn een Azure AD B2B-gebruiker en gast gebruiker synoniemen. Daarom wordt een Azure AD B2B-samenwerkings gebruiker toegevoegd als een gebruiker met User type = Guest standaard. In sommige gevallen is de partner organisatie echter lid van een grotere organisatie waar de host-organisatie ook deel van uitmaakt. Als dat het geval is, kan de organisatie van de host gebruikers in de partner organisatie beschouwen als leden in plaats van gasten. Gebruik de Api's van de Azure AD B2B-uitnodiging om een gebruiker toe te voegen aan of te uitnodigen van de partner organisatie als lid van de organisatie.

## <a name="filter-for-guest-users-in-the-directory"></a>Filteren op gast gebruikers in de Directory

![Scherm opname van het filter voor gast gebruikers](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>User type converteren
Het is mogelijk om User type van het ene naar het andere gast en vice versa te converteren met behulp van Power shell. De eigenschap User type vertegenwoordigt echter de relatie van de gebruiker met de organisatie. Daarom moet u deze eigenschap alleen wijzigen als de relatie van de gebruiker met de organisatie wordt gewijzigd. Als de relatie van de gebruiker verandert, moet de user principal name (UPN) worden gewijzigd? Moet de gebruiker toegang blijven houden tot dezelfde bronnen? Moet er een postvak worden toegewezen? Het is niet raadzaam om de User type te wijzigen met behulp van Power shell als een Atomic-activiteit. Als deze eigenschap wordt gebruikt in Power shell, is het niet raadzaam een afhankelijkheid van deze waarde te maken.

## <a name="remove-guest-user-limitations"></a>Gebruikers beperkingen van de gast verwijderen
Er zijn mogelijk situaties waarin u uw gast gebruikers hogere bevoegdheden wilt geven. U kunt een gast gebruiker toevoegen aan een wille keurige rol en zelfs de standaard gebruikers beperkingen van de gast gebruiker in de Directory verwijderen om een gebruiker dezelfde bevoegdheden te geven als leden.

Het is mogelijk om de standaard beperkingen uit te scha kelen, zodat een gast gebruiker in de bedrijfs Directory dezelfde machtigingen heeft als een lid van de gebruiker.

![Scherm afbeelding met de optie externe gebruikers in de gebruikers instellingen](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan ik gast gebruikers zichtbaar maken in de algemene adres lijst van Exchange?
Ja. Gast objecten zijn standaard niet zichtbaar in de algemene adres lijst van uw organisatie, maar u kunt Azure Active Directory Power shell gebruiken om ze zichtbaar te maken. Zie voor meer informatie ' gasten toevoegen aan de algemene adres lijst ' in het [artikel Microsoft 365 per groep-gast toegang](/microsoft-365/solutions/per-group-guest-access).

## <a name="can-i-update-a-guest-users-email-address"></a>Kan ik het e-mail adres van een gast gebruiker bijwerken?

Als een gast gebruiker uw uitnodiging accepteert en vervolgens het e-mail adres wijzigt, wordt de nieuwe e-mail niet automatisch gesynchroniseerd met het gast gebruikers object in uw Directory. De eigenschap mail wordt gemaakt via [Microsoft Graph-API](/graph/api/resources/user). U kunt de eigenschap e-mail bijwerken via de Microsoft Graph-API, het Exchange-beheer centrum of [Exchange Online Power shell](/powershell/module/exchange/users-and-groups/set-mailuser). De wijziging wordt weer gegeven in het gebruikers object van de Azure AD-gast.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
* [Gebruikers tokens voor B2B-samen werking](user-token.md)
* [Toewijzing van gebruikers claims voor B2B-samen werking](claims-mapping.md)
