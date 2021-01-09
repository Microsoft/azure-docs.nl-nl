---
title: 'De Azure Digital Apparaatdubbels-aanvraag is mislukt met de status: 403 (verboden)'
description: "Oorzaken en oplossingen voor de service aanvraag zijn mislukt. Status: 403 (verboden) ' op Azure Digital Apparaatdubbels."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: 1517c066fe20d478094f57d85d6e27f355a93601
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049810"
---
# <a name="service-request-failed-status-403-forbidden"></a>Service aanvraag mislukt. Status: 403 (verboden)

In dit artikel worden oorzaken en oplossings stappen beschreven voor het ontvangen van een 403-fout van service aanvragen naar Azure Digital Apparaatdubbels. 

## <a name="symptoms"></a>Symptomen

Deze fout kan optreden bij veel typen service aanvragen waarvoor verificatie is vereist. Het gevolg is dat de API-aanvraag mislukt en een fout status retourneert van `403 (Forbidden)` .

## <a name="causes"></a>Oorzaken

### <a name="cause-1"></a>Oorzaak #1

Deze fout geeft meestal aan dat uw op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) voor de service niet juist is ingesteld. Voor veel acties voor een Azure Digital Apparaatdubbels-exemplaar moet u de rol *Azure Digital apparaatdubbels-gegevens eigenaar* hebben **op het exemplaar dat u wilt beheren**. 

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

### <a name="cause-2"></a>Oorzaak #2

Als u een client-app gebruikt om te communiceren met Azure Digital Apparaatdubbels die wordt geverifieerd met een [app-registratie](how-to-create-app-registration.md), kan deze fout optreden omdat uw app-registratie geen machtigingen heeft ingesteld voor de Azure Digital apparaatdubbels-service.

De app-registratie moet toegangs machtigingen hebben die zijn geconfigureerd voor de Azure Digital Apparaatdubbels-Api's. Wanneer uw client-app vervolgens wordt geverifieerd op basis van de app-registratie, worden de machtigingen verleend die de app-registratie heeft geconfigureerd.

## <a name="solutions"></a>Oplossingen

### <a name="solution-1"></a>#1 oplossing

De eerste oplossing is om te controleren of uw Azure-gebruiker de rol _**Azure Digital Apparaatdubbels data owner**_ heeft voor het exemplaar dat u wilt beheren. Als u deze functie niet hebt, stelt u deze in.

Houd er rekening mee dat deze rol afwijkt van...
* de voormalige naam voor deze rol tijdens de preview-versie, de *Azure Digital apparaatdubbels-eigenaar (preview)* (de rol is hetzelfde, maar de naam is gewijzigd)
* de rol van *eigenaar* van het hele Azure-abonnement. De *Azure Digital apparaatdubbels-gegevens eigenaar* is een rol binnen Azure Digital apparaatdubbels en is afgestemd op dit individuele Azure Digital apparaatdubbels-exemplaar.
* de rol van *eigenaar* in azure Digital apparaatdubbels. Dit zijn twee verschillende Azure Digital Apparaatdubbels management-rollen en de *Azure Digital apparaatdubbels-gegevens eigenaar* is de rol die moet worden gebruikt voor beheer.

#### <a name="check-current-setup"></a>Huidige instellingen controleren

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Problemen oplossen 

Als u deze roltoewijzing niet hebt, moet iemand met een eigenaar van uw Azure- **abonnement** de volgende opdracht uitvoeren om uw Azure-gebruiker de rol *Azure Digital apparaatdubbels data owner* te geven op de **Azure Digital apparaatdubbels-instantie**. 

Als u een eigenaar van het abonnement bent, kunt u deze opdracht zelf uitvoeren. Als dat niet het geval is, neemt u contact op met een eigenaar om deze opdracht namens u uit te voeren.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

Voor meer informatie over deze functie vereiste en het toewijzings proces, zie de [sectie *toegangs machtigingen van uw gebruiker instellen*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) voor *instructies: een exemplaar en authenticatie instellen (CLI of portal)*.

Als u deze roltoewijzing al hebt *en* u een Azure AD-App-registratie gebruikt om een client-app te verifiëren, kunt u door gaan met de volgende oplossing als deze oplossing het 403-probleem niet heeft opgelost.

### <a name="solution-2"></a>#2 oplossing

Als u een Azure AD-App-registratie gebruikt om een client-app te verifiëren, is de tweede mogelijke oplossing om te controleren of de app-registratie machtigingen heeft die zijn geconfigureerd voor de Azure Digital Apparaatdubbels-service. Als deze niet zijn geconfigureerd, stelt u deze in op.

#### <a name="check-current-setup"></a>Huidige instellingen controleren

Als u wilt controleren of de machtigingen juist zijn geconfigureerd, gaat u naar de [pagina overzicht van Azure AD-App-registratie](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in de Azure Portal. U kunt deze pagina zelf openen door naar *app-registraties* te zoeken in de zoek balk van de portal.

Ga naar het tabblad *alle toepassingen* om alle app-registraties weer te geven die zijn gemaakt in uw abonnement.

U ziet nu de app-registratie die u zojuist hebt gemaakt in de lijst. Selecteer deze om de details ervan te openen.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="App-registraties pagina in de Azure Portal":::

Controleer eerst of de instellingen voor de Azure Digital Apparaatdubbels-machtigingen correct zijn ingesteld voor de registratie. Als u dit wilt doen, selecteert u *manifest* in de menu balk om de manifest code van de app-registratie weer te geven. Ga naar de onderkant van het code venster en zoek deze velden onder `requiredResourceAccess` . De waarden moeten overeenkomen met die in de onderstaande scherm afbeelding:

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Portal weergave van het manifest voor de Azure AD-App-registratie":::

Selecteer vervolgens *API-machtigingen* in de menu balk om te controleren of deze app-registratie Lees-en schrijf machtigingen voor Azure Digital apparaatdubbels bevat. U ziet een item als volgt:

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Portal weergave van de API-machtigingen voor de Azure AD-App-registratie, met lees-en schrijf toegang voor Azure Digital Apparaatdubbels":::

#### <a name="fix-issues"></a>Problemen oplossen

Als een van deze problemen anders lijkt dan wordt beschreven, volgt u de instructies voor het instellen van een app-registratie in een [*procedure: een app-registratie maken*](how-to-create-app-registration.md).

## <a name="next-steps"></a>Volgende stappen

Lees de installatie stappen voor het maken en verifiëren van een nieuw exemplaar van Azure Digital Apparaatdubbels:
* [*Instructies: een instantie en authenticatie instellen (CLI)*](how-to-set-up-instance-cli.md)

Lees meer over beveiliging en machtigingen op Azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)
