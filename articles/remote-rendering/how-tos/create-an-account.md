---
title: Een Azure Remote Rendering-account maken
description: Hierin worden de stappen beschreven voor het maken van een account voor externe rendering van Azure
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 83bd4a7ae0082d24f7ac617719e628f4db4baeb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98197626"
---
# <a name="create-an-azure-remote-rendering-account"></a>Een Azure Remote Rendering-account maken

In dit hoofd stuk wordt u begeleid bij de stappen voor het maken van een account voor de **Azure remote rendering** -service. Een geldig account is verplicht voor het volt ooien van de Quick starts of zelf studies.

## <a name="create-an-account"></a>Een account maken

De volgende stappen zijn nodig om een account te maken voor de Azure remote rendering-service:

1. Ga naar de [voorbeeld pagina Mixed Reality](https://aka.ms/MixedRealityPrivatePreview)
1. Klik op de knop een resource maken
1. Typ in het zoek veld ("zoeken op Marketplace") de tekst "externe rendering" en druk op ENTER.
1. Klik in de lijst met resultaten op de tegel externe Rendering
1. Klik in het volgende scherm op de knop maken. Er wordt een formulier geopend voor het maken van een nieuw externe rendering-account:
    1. ' Resource naam ' instellen op de naam van het account
    1. Abonnement indien nodig bijwerken
    1. De resource groep instellen op een resource groep van uw keuze
    1. Selecteer een regio in de vervolg keuzelijst locatie waarin deze resource moet worden gemaakt. Zie de opmerkingen bij [account regio's](create-an-account.md#account-regions) hieronder.
1. Zodra het account is gemaakt, navigeert u naar dit en:
    1. Bekijk op het tabblad *overzicht* de account-id
    1. Bekijk op het tabblad *instellingen > Access Keys* de ' primaire sleutel ': dit is de geheime account sleutel van het account

### <a name="account-regions"></a>Account regio's
De locatie die wordt opgegeven tijdens het maken van het account voor een account bepaalt aan welke regio de account resource is toegewezen. Dit kan niet worden gewijzigd nadat het is gemaakt. Het account kan echter worden gebruikt om verbinding te maken met een externe rendering-sessie in een [ondersteunde regio](./../reference/regions.md), ongeacht de locatie van het account.

### <a name="retrieve-the-account-information"></a>De account gegevens ophalen

Voor de voor beelden en zelf studies moet u de account-ID en een sleutel opgeven. Bijvoorbeeld in de **arrconfig.js** voor het bestand dat wordt gebruikt voor de Power shell-voorbeeld scripts:

```json
"accountSettings": {
    "arrAccountId": "<fill in the account ID from the Azure portal>",
    "arrAccountKey": "<fill in the account key from the Azure portal>",
    "region": "<select from available regions>"
},
```

Bekijk de [lijst met beschik bare regio's](../reference/regions.md) voor het invullen van de *regio* optie.

De waarden voor **`arrAccountId`** en **`arrAccountKey`** zijn te vinden in de portal, zoals beschreven in de volgende stappen:

* Ga naar de [Azure-portal](https://www.portal.azure.com)
* Zoek uw **"externe rendering-account"** -deze moet zich in de lijst met **recente resources** bevinden. U kunt er ook naar zoeken in de zoek balk bovenin. In dat geval moet u ervoor zorgen dat het abonnement dat u wilt gebruiken, is geselecteerd in het standaard abonnements filter (filter pictogram naast zoek balk):

![Abonnements filter](./media/azure-subscription-filter.png)

Als u op uw account klikt, gaat u naar dit scherm, waarin de  **account-id** direct wordt weer gegeven:

![Azure-account-ID](./media/azure-account-id.png)

Voor de sleutel selecteert u **toegangs sleutels** in het deel venster aan de linkerkant. Op de volgende pagina wordt een primaire en secundaire sleutel weer gegeven:

![Azure-toegangs sleutels](./media/azure-account-primary-key.png)

De waarde voor **`arrAccountKey`** kan een primaire of secundaire sleutel zijn.

## <a name="link-storage-accounts"></a>Opslag accounts koppelen

In deze alinea wordt uitgelegd hoe u opslag accounts koppelt aan uw externe rendering-account. Wanneer een opslag account is gekoppeld, is het niet nodig om elke keer dat u wilt communiceren met de gegevens in uw account, een SAS-URI te genereren, bijvoorbeeld bij het laden van een model. In plaats daarvan kunt u de namen van opslag accounts rechtstreeks gebruiken zoals beschreven in de [sectie een model laden](../concepts/models.md#loading-models).

De stappen in dit artikel moeten worden uitgevoerd voor elk opslag account dat deze toegangs methode moet gebruiken. Als u nog geen opslag accounts hebt gemaakt, kunt u de respectieve stap door lopen in de [Snelstartgids een model converteren voor rendering](../quickstarts/convert-model.md#storage-account-creation).

Hierbij wordt ervan uitgegaan dat u een opslag account hebt. Ga naar het opslag account in de portal en ga naar het tabblad **Access Control (IAM)** voor dat opslag account:

![IAM van opslag account](./media/azure-storage-account.png)

Zorg ervoor dat u eigenaars machtigingen hebt voor dit opslag account om ervoor te zorgen dat u roltoewijzingen kunt toevoegen. Als u geen toegang hebt, wordt de optie **toewijzing van rol toevoegen** uitgeschakeld.

Klik op de knop **toevoegen** in de tegel toewijzing van een rol toevoegen om de rol toe te voegen.

![Toewijzing van IAM van opslag account toevoegen](./media/azure-add-role-assignment.png)

* Wijs de rol **gegevensinzender voor opslag BLOB** toe, zoals weer gegeven in de bovenstaande scherm afbeelding.
* Selecteer **extern rendering-account**  systeem toegewezen beheerde identiteit in de vervolg keuzelijst **toegang toewijzen aan** .
* Selecteer uw abonnement en het account voor externe rendering in de laatste vervolg keuzelijst.
* Klik op Opslaan om uw wijzigingen op te slaan.

> [!WARNING]
> Als uw externe rendering-account niet wordt weer gegeven, raadpleegt u de [sectie problemen oplossen](../resources/troubleshoot.md#cant-link-storage-account-to-arr-account).

> [!IMPORTANT]
> Azure-roltoewijzingen worden in de cache opgeslagen door Azure Storage. er kan dus een vertraging van Maxi maal 30 minuten optreden wanneer u toegang verleent aan uw externe rendering-account en wanneer het kan worden gebruikt om toegang te krijgen tot uw opslag account. Zie de [documentatie voor Azure op rollen gebaseerde toegangs beheer (Azure RBAC)](../../role-based-access-control/troubleshooting.md#role-assignment-changes-are-not-being-detected) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie](authentication.md)
* [De Azure frontend-Api's gebruiken voor verificatie](frontend-apis.md)
* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)