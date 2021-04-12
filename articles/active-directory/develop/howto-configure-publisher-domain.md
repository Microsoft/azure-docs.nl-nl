---
title: Het Uitgever domein van een app configureren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het configureren van het domein van de uitgever van een toepassing om gebruikers te laten weten waar hun gegevens worden verzonden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 9b3840ffb39a204cfa24dcb0430f20ac16587582
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104175"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Procedure: het domein van de uitgever van een toepassing configureren

Het domein van de uitgever van een toepassing wordt weer gegeven aan gebruikers op de [toestemming prompt van de toepassing](application-consent-experience.md) om gebruikers te laten weten waar hun gegevens worden verzonden. Multi tenant-toepassingen die na 21 mei 2019 zijn geregistreerd en die geen Publisher-domein hebben, worden weer gegeven als niet- **geverifieerd**. Toepassingen met meerdere tenants zijn toepassingen die ondersteuning bieden voor accounts buiten één organisatie Directory. u kunt bijvoorbeeld alle Azure AD-accounts ondersteunen of alle Azure AD-accounts en persoonlijke micro soft-accounts ondersteunen.

## <a name="new-applications"></a>Nieuwe toepassingen

Wanneer u een nieuwe app registreert, kan het uitgevers domein van uw app worden ingesteld op een standaard waarde. De waarde is afhankelijk van waar de app is geregistreerd, met name of de app is geregistreerd in een Tenant en of de Tenant geverifieerde domeinen heeft.

Als er domeinen met tenants zijn geverifieerd, wordt het Uitgever domein van de app standaard ingesteld op het primaire geverifieerde domein van de Tenant. Als er geen geverifieerde Tenant domeinen zijn (dit is het geval wanneer de toepassing niet is geregistreerd in een Tenant), wordt het Uitgever domein van de app ingesteld op null.

De volgende tabel bevat een overzicht van het standaard gedrag van de domein waarde van de uitgever.  

| Door tenants geverifieerde domeinen | Standaard waarde van het uitgevers domein |
|-------------------------|----------------------------|
| null | null |
| *. onmicrosoft.com | *. onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (primair) | domain2.com |

Als het Publisher-domein van een multi tenant-toepassing niet is ingesteld, of als het is ingesteld op een domein dat eindigt op. onmicrosoft.com, geeft de toestemming prompt van de app niet **geverifieerd** in plaats van het domein van de uitgever.

## <a name="grandfathered-applications"></a>Grandfathered-toepassingen

Als uw app is geregistreerd vóór 21 mei 2019, wordt de vraag van de toestemming van uw toepassing **niet weer gegeven** als u geen uitgever domein hebt ingesteld. We raden u aan de domein waarde van de uitgever zo in te stellen dat gebruikers deze informatie kunnen zien op de toestemming prompt van uw app.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Het domein van de uitgever configureren met behulp van de Azure Portal

Voer de volgende stappen uit om het Uitgever domein van uw app in te stellen.

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de Tenant te selecteren waarin de app is geregistreerd.
1. Navigeer naar [Azure Active Directory > app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) om de app te zoeken en te selecteren die u wilt configureren.

   Zodra u de app hebt geselecteerd, ziet u de **overzichts** pagina van de app.
1. Selecteer onder **beheren** de **huis stijl**.
1. Zoek het **domein** veld van de uitgever en selecteer een van de volgende opties:

   - Selecteer **een domein configureren** als u nog geen domein hebt geconfigureerd.
   - Selecteer **domein bijwerken** als er al een domein is geconfigureerd.

Als uw app is geregistreerd in een Tenant, worden er twee tabbladen weer geven waaruit u kunt kiezen: **Selecteer een geverifieerd domein** en **Controleer of er een nieuw domein** is.

Als uw app niet is geregistreerd in een Tenant, ziet u alleen de optie om een nieuw domein voor uw toepassing te controleren.

### <a name="to-verify-a-new-domain-for-your-app"></a>Een nieuw domein voor uw app verifiëren

1. Maak een bestand `microsoft-identity-association.json` met de naam en plak het volgende JSON-code fragment.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. Vervang de tijdelijke aanduiding *{your-app-id-hier}* door de client-id van de toepassing die overeenkomt met uw app.

1. Host het bestand op: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` . Vervang de tijdelijke aanduiding *{uw domein-hier}* zodat deze overeenkomt met het geverifieerde domein.

1. Klik op de knop **domein controleren en opslaan** .

### <a name="to-select-a-verified-domain"></a>Een geverifieerd domein selecteren

- Als uw Tenant domeinen heeft geverifieerd, selecteert u een van de domeinen in de vervolg keuzelijst **Selecteer een geverifieerd domein** .

>[!Note]
> De verwachte header content-type die moet worden geretourneerd, is `application/json` . Er wordt mogelijk een fout melding weer gegeven die hieronder wordt vermeld als u iets anders als gebruikt `application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Implicaties voor de toestemming om de app te vragen

Het configureren van het uitgevers domein heeft invloed op wat gebruikers te zien krijgen op de vraag om toestemming van de app. Meer informatie over de onderdelen van de toestemming prompt vindt u in [inzicht in de ervaring voor de toestemming van de toepassing](application-consent-experience.md).

De volgende tabel beschrijft het gedrag voor toepassingen die zijn gemaakt vóór 21 mei 2019.

![Vraag toestemming voor apps die zijn gemaakt vóór 21 mei 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Het gedrag voor nieuwe toepassingen die na 21 mei 2019 worden gemaakt, is afhankelijk van het domein van de uitgever en het type toepassing. In de volgende tabel worden de wijzigingen beschreven die u verwacht te zien met de verschillende combi Naties van configuraties.

![Toestemming vragen voor apps die zijn gemaakt na 21 mei 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Implicaties voor omleidings-Uri's

Toepassingen die zich aanmelden bij gebruikers met een werk-of school account of persoonlijke micro soft[-accounts (multi tenant](single-and-multi-tenant-apps.md)), zijn onderhevig aan enkele beperkingen bij het opgeven van omleidings-uri's.

### <a name="single-root-domain-restriction"></a>Beperking van één hoofd domein

Wanneer de domein waarde van de uitgever voor multi tenant-apps is ingesteld op NULL, kunnen apps worden beperkt tot het delen van één hoofd domein voor de omleidings-Uri's. De volgende combi natie van waarden is bijvoorbeeld niet toegestaan omdat het hoofd domein, contoso.com, niet overeenkomt met fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Beperkingen voor subdomeinen

Subdomeinen zijn toegestaan, maar u moet het hoofd domein expliciet registreren. Bijvoorbeeld, terwijl de volgende Uri's één hoofd domein delen, is de combi natie niet toegestaan.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Als de ontwikkelaar echter expliciet het hoofd domein toevoegt, is de combi natie toegestaan.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Uitzonderingen

Voor de volgende gevallen gelden de beperkingen voor één hoofd domein niet:

- Apps met één Tenant of apps die zijn gericht op accounts in één map
- Gebruik van localhost als omleidings-Uri's
- Uri's omleiden met aangepaste schema's (niet-HTTP of HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Het Uitgever domein via een programma configureren

Er is momenteel geen REST API-of Power Shell-ondersteuning voor het configureren van het Publisher-domein via een programma.
