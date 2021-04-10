---
title: 'Quickstart: Een web-API registreren en weergeven | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart registreert u een web-API met het Microsoft Identity Platform en configureert u de bereiken, zodat clients toegang hebben tot de resources van de API op basis van machtigingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 820bc7dfe9123db495c151cd5cd0ea5ae337619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103971"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Quickstart: Een toepassing configureren om een web-API beschikbaar te maken

In deze quickstart registreert u een web-API met het Microsoft Identity Platform en maakt u de web-API beschikbaar voor clienttoepassingen door een voorbeeldbereik toe te voegen. Door de Web-API te registreren en deze via bereiken weer te geven, kunt u toegang tot de resources op basis van machtigingen verlenen aan geautoriseerde gebruikers en clienttoepassingen die toegang hebben tot uw API.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement - [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Voltooiing van [quickstart: Een tenant instellen](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>De web-API registreren

Als u toegang via bereiken tot de resources in uw web-API wilt instellen, moet u de API eerst registreren bij het Microsoft Identity Platform.

1. Voer de stappen uit in de sectie **Een toepassing registreren** in de [Quickstart: Een app registreren bij het Microsoft Identity Platform](quickstart-register-app.md).
1. Sla de secties **Een omleidings-URI toevoegen** en **Platforminstellingen configureren** over. U hoeft geen omleidings-URI voor een web-API te configureren omdat er geen gebruiker interactief is aangemeld.
1. Sla de sectie **Aanmeldingsgegevens toevoegen** voorlopig over. Alleen als uw API toegang heeft tot een downstream API, heeft deze eigen aanmeldingsgegevens nodig, een scenario dat niet in dit artikel wordt besproken.

Als uw web-API is geregistreerd, bent u klaar om de bereiken toe te voegen die de code van uw API kan gebruiken om een gedetailleerde machtiging te bieden aan gebruikers van uw API.

## <a name="add-a-scope"></a>Een bereik toevoegen

De code in een clienttoepassing vraagt machtigingen aan voor het uitvoeren van bewerkingen die zijn gedefinieerd door de web-API door een toegangstoken en de bijbehorende aanvragen aan de beveiligde bron (de web-API) door te geven. Uw web-API voert de aangevraagde bewerking alleen uit als het toegangstoken dat wordt ontvangen, de bereiken bevat die voor de bewerking vereist zijn.

Voer eerst de volgende stappen uit om een voorbeeldbereik te maken met de naam `Employees.Read.All`:

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren die de registratie van uw clienttoepassing bevat.
1. Selecteer **Azure Active Directory** > **App-registraties** en selecteer de app-registratie van uw API.
1. Selecteer **Een API weergeven** > **Een bereik toevoegen**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Het deelvenster Een API weergeven van een app-registratie in de Azure-portal":::

1. U wordt gevraagd een **URI voor de app-ID** in te stellen als u die nog niet hebt geconfigureerd.

   De URI van de App-ID fungeert als voorvoegsel voor de bereiken waarnaar u verwijst in de code van de API en moet globaal uniek zijn. U kunt de opgegeven standaardwaarde gebruiken, in de vorm `api://<application-client-id>`, of een meer leesbare URI opgeven, zoals `https://contoso.com/api`.

1. Geef vervolgens de kenmerken van het bereik op in het deelvenster **Een bereik toevoegen**. Voor deze procedure kunt u de voorbeeldwaarden gebruiken of uw eigen waarden opgeven.

    | Veld | Beschrijving | Voorbeeld |
    |-------|-------------|---------|
    | **Naam van bereik** | De naam van uw bereik. Een algemene naamconventie voor bereiken is `resource.operation.constraint`. | `Employees.Read.All` |
    | **Wie kan toestemming verlenen?** | Of gebruikers toestemming kunnen verlenen voor dit bereik, of dat toestemming van de beheerder is vereist. Selecteer **Alleen beheerders** voor machtigingen met meer bevoegdheden. | **Beheerders en gebruikers** |
    | **Weergavenaam van beheerderstoestemming** | Een korte beschrijving van het doel van het bereik die alleen beheerders te zien krijgen. | `Read-only access to Employee records` |
    | **Beschrijving van beheerderstoestemming** | Een uitvoeriger beschrijving van de machtiging die door het bereik wordt verleend en die alleen aan beheerders wordt weergegeven. | `Allow the application to have read-only access to all Employee data.` |
    | **Weergavenaam van gebruikerstoestemming** | Een korte beschrijving van het doel van het bereik. Wordt alleen aan gebruikers weergegeven als u **Wie kan instemmen met deze machtiging** hebt ingesteld op **Beheerders en gebruikers**. | `Read-only access to your Employee records` |
    | **Beschrijving van gebruikerstoestemming** | Een uitvoeriger beschrijving van de machtiging die door het bereik wordt verleend. Wordt alleen aan gebruikers weergegeven als u **Wie kan instemmen met deze machtiging** hebt ingesteld op **Beheerders en gebruikers**. | `Allow the application to have read-only access to your Employee data.` |

1. Stel de **Status** in op **Ingeschakeld** en selecteer **Bereik toevoegen**.

1. (Optioneel) Als u vragen om toestemming door gebruikers van uw toepassing wilt onderdrukken voor de bereiken die u hebt gedefinieerd, kunt u de clienttoepassing *vooraf autoriseren* voor toegang tot uw web-API. U moet *uitsluitend* die clienttoepassingen vooraf autoriseren die u vertrouwt, omdat uw gebruikers niet de mogelijkheid hebben om toestemming te weigeren.
    1. Selecteer onder **Geautoriseerde clienttoepassingen** de optie **Een clienttoepassing toevoegen**
    1. Voer de **toepassings-id (client)** in van de client-toepassing die u vooraf wilt autoriseren. Bijvoorbeeld een web-toepassing die u eerder hebt geregistreerd.
    1. Selecteer onder **Geautoriseerde bereiken** de bereiken waarvoor u de vraag om toestemming wilt onderdrukken en selecteer vervolgens **Toepassing toevoegen**.

    Als u deze optionele stap hebt gevolgd, is de clienttoepassing nu een vooraf geautoriseerde clienttoepassing (PCA) en worden gebruikers niet om toestemming gevraagd wanneer ze zich aanmelden.

## <a name="add-a-scope-requiring-admin-consent"></a>Een bereik toevoegen waarvoor toestemming van de beheerder nodig is

Voeg vervolgens nog een voorbeeldbereik toe met de naam `Employees.Write.All` waarvoor alleen beheerders toestemming kunnen verlenen. Bereiken waarvoor toestemming van beheerders nodig is, worden doorgaans gebruikt voor toegang tot bewerkingen waarvoor een speciale machtiging nodig is, en worden vaak gebruikt door clienttoepassingen die worden uitgevoerd als back-endservices of daemons die een gebruiker niet interactief aanmelden.

Als u het `Employees.Write.All`-voorbeeldbereik wilt toevoegen, volgt u de stappen in de sectie [Een bereik toevoegen](#add-a-scope) en geeft u deze waarden op in het deelvenster **Een bereik toevoegen**:

| Veld                          | Voorbeeldwaarde                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Naam van bereik**                 | `Employees.Write.All`                                              |
| **Wie kan toestemming verlenen?**            | **Alleen beheerders**                                                    |
| **Weergavenaam van beheerderstoestemming** | `Write access to Employee records`                                 |
| **Beschrijving van beheerderstoestemming**  | `Allow the application to have write access to all Employee data.` |
| **Weergavenaam van gebruikerstoestemming**  | *Geen (leeg laten)*                                               |
| **Beschrijving van gebruikerstoestemming**   | *Geen (leeg laten)*                                               |

## <a name="verify-the-exposed-scopes"></a>De beschikbaar gemaakte bereiken verifiëren

Als u beide voorbeelden uit de vorige secties hebt toegevoegd, worden deze weergegeven in het deelvenster **Een API weergeven** van de app-registratie van uw web-API, zoals in deze afbeelding:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Schermopname van het deelvenster Een API weergeven, waarin twee bereiken worden weergegeven.":::

Zoals in de afbeelding wordt weergegeven, is de volledige tekenreeks van een bereik een samenvoeging van de **URI voor de app-ID** van uw web-API en de **Bereiknaam** van het bereik.

Als de URI voor de app-ID van uw web-API bijvoorbeeld `https://contoso.com/api` is en uw bereiknaam `Employees.Read.All` is, is dit het volledige bereik:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>De beschikbaar gemaakte bereiken gebruiken

In het volgende artikel van de serie configureert u de registratie van een clienttoepassing met toegang tot uw web-API en de bereiken die u hebt gedefinieerd door de stappen in dit artikel te volgen.

Zodra de registratie van een clienttoepassing is gemachtigd om toegang te krijgen tot uw web-API, kan de client een OAuth 2.0-toegangstoken krijgen van het Microsoft Identity Platform. Wanneer de client de web-API aanroept, wordt een toegangstoken weergegeven waarvan de bereikclaim (`scp`) is ingesteld op de machtigingen die u hebt opgegeven in de app-registratie van de client.

U kunt aanvullende bereiken indien nodig later weergeven. Houd er rekening mee dat uw web-API meerdere bereiken kan weergeven die aan verschillende bewerkingen gekoppeld zijn. Via de resource kan toegang tot de web-API tijdens runtime worden beheerd, door het evalueren van de bereikclaim(s) (`scp`) in het ontvangen OAuth 2.0-toegangstoken.

## <a name="next-steps"></a>Volgende stappen

Nu u uw web-API beschikbaar hebt gemaakt door de bereiken ervan te configureren, configureert u de registratie van uw clienttoepassing met machtigingen voor toegang tot de bereiken.

> [!div class="nextstepaction"]
> [Een app-registratie configureren voor toegang tot de web-API](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
