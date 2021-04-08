---
title: 'Azure AD Connect synchronisatie: Directory-extensies | Microsoft Docs'
description: In dit onderwerp wordt de functie Directory-extensies in Azure AD Connect beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4152783129fa1c5950d6cf6287332bf90d32a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97976874"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect synchronisatie: Directory-extensies
U kunt Directory-extensies gebruiken om het schema uit te breiden in Azure Active Directory (Azure AD) met uw eigen kenmerken van on-premises Active Directory. Met deze functie kunt u LOB-apps bouwen door gebruik te maken van kenmerken die u on-premises blijft beheren. Deze kenmerken kunnen worden gebruikt via [uitbrei dingen](/graph/extensibility-overview
). U kunt de beschik bare kenmerken weer geven met behulp van [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). U kunt deze functie ook gebruiken om dynamische groepen te maken in azure AD.

Op dit moment gebruikt geen Microsoft 365 werk belasting deze kenmerken.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Aanpassen welke kenmerken moeten worden gesynchroniseerd met Azure AD

U kunt configureren welke extra kenmerken u wilt synchroniseren in het pad voor aangepaste instellingen in de installatie wizard.

> [!NOTE]
> In Azure AD Connect eerdere versies dan 1.2.65.0 is het zoekvak voor **beschik bare kenmerken** hoofdletter gevoelig.

![Wizard schema-uitbrei ding](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

In de installatie worden de volgende kenmerken weer gegeven. Dit zijn geldige kandidaten:

* Object typen voor gebruikers en groepen
* Kenmerken met één waarde: teken reeks, Booleaanse waarde, geheel getal, binair
* Kenmerken met meerdere waarden: teken reeks, binair


>[!NOTE]
> Nadat Azure AD Connect gesynchroniseerd kenmerk met meerdere waarden Active Directory naar Azure AD als een uitbrei ding met meerdere waarden is toegevoegd, is het mogelijk om een kenmerk toe te voegen aan de SAML-claim. Maar het is niet mogelijk om deze gegevens via een API-aanroep te gebruiken.

De lijst met kenmerken wordt gelezen uit de schema cache die is gemaakt tijdens de installatie van Azure AD Connect. Als u het Active Directory schema met aanvullende kenmerken hebt uitgebreid, moet u [het schema vernieuwen](how-to-connect-installation-wizard.md#refresh-directory-schema) voordat deze nieuwe kenmerken zichtbaar zijn.

Een object in azure AD kan Maxi maal 100 kenmerken voor Directory-extensies hebben. De maximale lengte is 250 tekens. Als een kenmerk waarde langer is, wordt deze door de synchronisatie-engine afgekapt.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Configuratie wijzigingen in azure AD gemaakt door de wizard

Tijdens de installatie van Azure AD Connect wordt een toepassing geregistreerd waar deze kenmerken beschikbaar zijn. U kunt deze toepassing weer geven in de Azure Portal. De naam is altijd de **app voor Tenant-schema-uitbrei ding**.

![App voor schema-uitbrei ding](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Zorg ervoor dat u **alle toepassingen** selecteert om deze app weer te geven.

De kenmerken worden voorafgegaan door de **extensie \_ {ApplicationId} \_**. ApplicationId heeft dezelfde waarde voor alle kenmerken in uw Azure AD-Tenant. U hebt deze waarde nodig voor alle andere scenario's in dit onderwerp.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Kenmerken weer geven met behulp van de Microsoft Graph-API

Deze kenmerken zijn nu beschikbaar via de Microsoft Graph-API met behulp van [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> In de Microsoft Graph-API moet u vragen om de kenmerken te retour neren. Selecteer de kenmerken als volgt expliciet: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> Zie [Microsoft Graph: query parameters gebruiken](/graph/query-parameters#select-parameter)voor meer informatie.

>[!NOTE]
> Het synchroniseren van kenmerk waarden van AADConnect naar extensie kenmerken die niet zijn gemaakt door AADConnect, wordt niet ondersteund. Dit kan leiden tot prestatie problemen en onverwachte resultaten. Alleen extensie kenmerken die worden gemaakt zoals weer gegeven in het bovenstaande, worden ondersteund voor synchronisatie.

## <a name="use-the-attributes-in-dynamic-groups"></a>De kenmerken in dynamische groepen gebruiken

Een van de handigste scenario's is het gebruik van deze kenmerken in dynamische beveiligings-of Microsoft 365 groepen.

1. Maak een nieuwe groep in azure AD. Geef het een goede naam en zorg ervoor dat het **lidmaatschaps type** een **dynamische gebruiker** is.

   ![Scherm afbeelding met een nieuwe groep](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selecteer deze optie om een **dynamische query toe te voegen**. Als u de eigenschappen bekijkt, worden deze uitgebreide kenmerken niet weer geven. U moet ze eerst toevoegen. Klik op **aangepaste extensie eigenschappen ophalen**, voer de toepassings-id in en klik op **Eigenschappen vernieuwen**.

   ![Scherm opname van Directory-extensies toegevoegd](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Open de vervolg keuzelijst eigenschap en houd er rekening mee dat de kenmerken die u hebt toegevoegd, nu zichtbaar zijn.

   ![Scherm afbeelding met nieuwe kenmerken die in de gebruikers interface worden weer gegeven](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Voltooi de expressie om uw vereisten aan te passen. In ons voor beeld is de regel ingesteld op **(User.extension_9d98ed114c4840d298fad781915f27e4_division-EQ ' verkoop en marketing ')**.

4. Nadat de groep is gemaakt, geeft u Azure AD enige tijd om de leden in te vullen en bekijkt u de leden.

   ![Scherm afbeelding met leden in de dynamische groep](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
