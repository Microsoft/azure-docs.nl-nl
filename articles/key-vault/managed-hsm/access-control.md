---
title: Toegangs beheer voor Azure Managed HSM
description: Toegangs machtigingen voor door Azure beheerde HSM en sleutels beheren. Behandelt het verificatie-en autorisatie model voor beheerde HSM en hoe u uw Hsm's kunt beveiligen.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 0c0a0c5f62f92aaf195e207dfd505ffb017d924e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653897"
---
# <a name="managed-hsm-access-control"></a>Toegangsbeheer van beheerde HSM

> [!NOTE]
> Key Vault resource provider ondersteunt twee resource typen: **kluizen** en **beheerde hsm's**. Toegangs beheer dat in dit artikel wordt beschreven, is alleen van toepassing op **beheerde hsm's**. Zie voor meer informatie over toegangs beheer voor beheerde HSM [toegang bieden tot Key Vault sleutels, certificaten en geheimen met een op rollen gebaseerd toegangs beheer van Azure](../general/rbac-guide.md).

Door Azure Key Vault beheerde HSM is een cloudservice die versleutelingssleutels beveiligt. Omdat deze gegevens vertrouwelijk en bedrijfskritiek zijn, is het belangrijk om de toegang tot uw beheerde HSM's te beveiligen, zodat alleen gemachtigde toepassingen en gebruikers toegang hebben tot uw beheerde HSM's. Dit artikel bevat een overzicht van het toegangsbeheermodel voor beheerde HSM's. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw beheerde HSM's kunt beveiligen.

## <a name="access-control-model"></a>Toegangsbeheermodel

Toegang tot een beheerde HSM wordt beheerd via twee interfaces: het **beheer vlak** en het **gegevens vlak**. Het beheer vlak is waar u de HSM zelf beheert. Bewerkingen in dit vlak zijn onder andere het maken en verwijderen van beheerde Hsm's en het ophalen van beheerde HSM-eigenschappen. Het gegevens vlak is waar u werkt met de gegevens die zijn opgeslagen in een beheerde HSM. Dit zijn versleutelings sleutels met HSM-back-ups. U kunt sleutels toevoegen, verwijderen, wijzigen en gebruiken voor het uitvoeren van cryptografische bewerkingen, het beheren van roltoewijzingen voor het beheren van de toegang tot de sleutels, het maken van een volledige HSM-back-up, het herstellen van een volledige back-up en het beheren van het beveiligings domein vanuit de gegevenslaag interface.

Voor toegang tot een beheerde HSM in een van beide vlieg tuigen moeten alle bellers de juiste verificatie en autorisatie hebben. Met verificatie wordt de identiteit van de aanroeper bepaald. Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Een aanroeper kan bestaan uit een van de [beveiligings-principals](../../role-based-access-control/overview.md#security-principal) die zijn gedefinieerd in azure Active Directory-gebruiker, groep, Service-Principal of beheerde identiteit.

Beide plannen gebruiken Azure Active Directory voor verificatie. Voor verificatie gebruiken ze verschillende systemen als volgt:
- Het beheer vlak maakt gebruik van Azure op rollen gebaseerd toegangs beheer--Azure RBAC--een autorisatie systeem dat is gebouwd op Azure Azure Resource Manager 
- Het gegevens vlak maakt gebruik van een beheerde RBAC (beheerde HSM Local RBAC): een autorisatie systeem dat is geïmplementeerd en afgedwongen op het niveau van het beheerde HSM.

Wanneer een beheerde HSM wordt gemaakt, biedt de aanvrager ook een lijst met beheerders van gegevens vlak (alle [beveiligings-principals](../../role-based-access-control/overview.md#security-principal) worden ondersteund). Alleen deze beheerders hebben toegang tot het beheerde HSM-gegevens vlak voor het uitvoeren van belang rijke bewerkingen en het beheren van roltoewijzingen (beheerde HSM Local RBAC).

Het machtigings model voor beide vlakken maakt gebruik van dezelfde syntaxis, maar ze worden afgedwongen op verschillende niveaus en roltoewijzingen maken gebruik van verschillende bereiken. Beheer vlak Azure RBAC wordt afgedwongen door Azure Resource Manager terwijl het gegevenslaag beheerde HSM lokale RBAC wordt afgedwongen door de beheerde HSM zelf.

> [!IMPORTANT]
> Het verlenen van een Security Principal Management-vlak toegang tot een beheerde HSM heeft geen toegang tot het gegevens vlak om toegang te krijgen tot sleutels of roltoewijzingen voor gegevenslaag die worden beheerd HSM Local RBAC). Deze isolatie is zo ontworpen dat onbedoelde uitbrei ding van bevoegdheden die invloed hebben op de toegang tot sleutels die zijn opgeslagen in een beheerde HSM wordt voor komen

Zo kan een abonnements beheerder (omdat ze de machtiging ' Inzender ' hebben voor alle resources in het abonnement) een beheerde HSM verwijderen in hun abonnement, maar als ze geen gegevens vlak toegang hebben via beheerde HSM Local RBAC, hebben ze geen toegang tot sleutels of kunnen ze geen roltoewijzing beheren in de beheerde HSM om zichzelf of anderen toegang tot het gegevens vlak te verlenen.

## <a name="azure-active-directory-authentication"></a>Verificatie via Azure Active Directory

Wanneer u een beheerde HSM maakt in een Azure-abonnement, wordt deze automatisch gekoppeld aan de Azure Active Directory Tenant van het abonnement. Alle bellers in beide abonnementen moeten worden geregistreerd in deze Tenant en moeten worden geverifieerd om toegang te krijgen tot de beheerde HSM.

De toepassing wordt geverifieerd met Azure Active Directory voordat u een van beide vlieg tuigen aanroept. De toepassing kan elke [ondersteunde verificatie methode](../../active-directory/develop/authentication-vs-authorization.md) gebruiken op basis van het toepassings type. De toepassing verkrijgt een token voor een resource in het vlak om toegang te krijgen. De resource is een eind punt in het beheer-of gegevens vlak, gebaseerd op de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API aanvraag naar het beheerde HSM-eind punt. Bekijk voor meer informatie de [volledige verificatie stroom](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Het gebruik van één verificatie mechanisme voor beide abonnementen heeft verschillende voor delen:

- Organisaties kunnen de toegang centraal beheren voor alle beheerde Hsm's in hun organisatie.
- Als een gebruiker deze verlaat, gaan ze onmiddellijk toegang tot alle beheerde Hsm's in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in Azure Active Directory, zoals om multi-factor Authentication in te scha kelen voor extra beveiliging.

## <a name="resource-endpoints"></a>Resource-eind punten

Beveiligings-principals hebben toegang tot de abonnementen via eind punten. De toegangs controle voor de twee abonnementen werkt afzonderlijk. Als u een toepassing toegang wilt verlenen voor het gebruik van sleutels in een beheerde HSM, geeft u toegang tot het gegevens vlak door beheerde HSM lokale RBAC te gebruiken. Als u een gebruiker toegang wilt verlenen tot een beheerde HSM-resource om de beheerde Hsm's te maken, lezen, verwijderen, verplaatsen en andere eigenschappen en Tags bewerken, gebruikt u Azure RBAC.

De volgende tabel bevat de eind punten voor de beheer-en gegevens abonnementen.

| Toegangs &nbsp; vlak | Eindpunten voor toegang | Operations | Mechanisme voor toegangsbeheer |
| --- | --- | --- | --- |
| Beheerlaag | **Wereldwijd:**<br> management.azure.com:443<br> | Beheerde Hsm's maken, lezen, bijwerken, verwijderen en verplaatsen<br>Beheerde HSM-Tags instellen | Azure RBAC |
| Gegevenslaag | **Wereldwijd:**<br> &lt;HSM-name &gt; . managedhsm.Azure.net:443<br> | **Sleutels**: ontsleutelen, versleutelen,<br> uitpakken, terugloop, controleren, ondertekenen, ophalen, weer geven, bijwerken, maken, importeren, verwijderen, back-up, herstellen, opschonen<br/><br/> Rol van gegevenslaag **-beheer (beheerde HSM Local RBAC)**_: roldefinities weer geven, rollen toewijzen, roltoewijzingen verwijderen, aangepaste rollen <br/> <br/> definiëren_* back-ups maken/herstellen **: back-up maken, herstellen, <br/> <br/> status back-** up maken/herstellen van beveiligings domein * *: beveiligings domein downloaden en uploaden | Beheerde HSM lokale RBAC |
|||||
## <a name="management-plane-and-azure-rbac"></a>Beheer vlak en Azure RBAC

In het beheer vlak gebruikt u Azure RBAC om de bewerkingen te autoriseren die een aanroeper kan uitvoeren. In het model van Azure RBAC heeft elk Azure-abonnement een exemplaar van Azure Active Directory. U verleent toegang aan gebruikers, groepen en toepassingen vanuit deze map. Toegang is verleend om resources te beheren in het Azure-abonnement dat gebruikmaakt van het Azure Resource Manager-implementatie model. Als u toegang wilt verlenen, gebruikt u de [Azure Portal](https://portal.azure.com/), de [Azure cli](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de [Azure Resource Manager rest-api's](/rest/api/authorization/roleassignments).

U maakt een sleutel kluis in een resource groep en beheert de toegang met behulp van Azure Active Directory. U verleent gebruikers of groepen de mogelijkheid om de sleutel kluizen in een resource groep te beheren. U verleent de toegang op een specifiek Scope niveau door de juiste Azure-rollen toe te wijzen. Als u toegang wilt verlenen aan een gebruiker om sleutel kluizen te beheren, wijst u een vooraf gedefinieerde `key vault Contributor` rol toe aan de gebruiker op een specifiek bereik. De volgende Scope niveaus kunnen worden toegewezen aan een Azure-rol:

- **Beheer groep**: een Azure-rol die is toegewezen op het abonnements niveau, is van toepassing op alle abonnementen in die beheer groep.
- **Abonnement**: een Azure-rol die is toegewezen op abonnements niveau, is van toepassing op alle resource groepen en resources in dat abonnement.
- **Resource groep**: een Azure-rol die is toegewezen op het niveau van de resource groep, is van toepassing op alle resources in die resource groep.
- **Specifieke resource**: een Azure-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutel kluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften voldoet, kunt u uw eigen rol definiëren. Zie [Azure RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)voor meer informatie.

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Gegevens vlak en beheerde HSM lokale RBAC

U verleent een beveiligingsprincipal toegang om specifieke sleutel bewerkingen uit te voeren door een rol toe te wijzen. Voor elke roltoewijzing moet u een rol en bereik opgeven waarop de toewijzing van toepassing is. Voor beheerde HSM Local RBAC twee scopes zijn beschikbaar.

- **"/" of "/Keys"**: het bereik van de HSM-niveau. Beveiligings-principals waaraan een rol in dit bereik is toegewezen, kunnen de bewerkingen uitvoeren die in de rol zijn gedefinieerd voor alle objecten (sleutels) in de beheerde HSM.
- **"/Keys/ &lt; key-name &gt; "**: bereik op sleutel niveau. Beveiligings-principals waaraan een rol in dit bereik is toegewezen, kunnen de bewerkingen uitvoeren die in deze rol zijn gedefinieerd voor alle versies van de opgegeven sleutel.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is beheerde HSM?](overview.md) voor een inleidende zelfstudie voor beheerders.
- Zie [beheerde HSM Local RBAC](role-management.md) voor een zelf studie over het beheer van rollen.
- Zie [Logboekregistratie van beheerde HSM](logging.md) voor meer informatie over logboekregistratie van het gebruik van beheerde HSM's.