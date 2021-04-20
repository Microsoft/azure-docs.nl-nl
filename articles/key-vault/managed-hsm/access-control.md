---
title: Toegangsbeheer voor Azure Managed HSM
description: Toegangsmachtigingen voor Azure Managed HSM en sleutels beheren. Behandelt het verificatie- en autorisatiemodel voor beheerde HSM en hoe u uw HSM's beveiligt.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: bea1ccf0777c6325bc86c15e0f88304c465d89c9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750280"
---
# <a name="managed-hsm-access-control"></a>Toegangsbeheer van beheerde HSM

> [!NOTE]
> Key Vault resourceprovider ondersteunt twee resourcetypen: **kluizen en** **beheerde HMS's.** Toegangsbeheer dat in dit artikel wordt beschreven, is alleen van toepassing **op beheerde HMS's.** Zie Toegang bieden tot Key Vault-sleutels, certificaten en geheimen met op rollen gebaseerd toegangsbeheer van Azure voor meer informatie over [toegangsbeheer voor beheerde](../general/rbac-guide.md)HSM.

Door Azure Key Vault beheerde HSM is een cloudservice die versleutelingssleutels beveiligt. Omdat deze gegevens vertrouwelijk en bedrijfskritiek zijn, is het belangrijk om de toegang tot uw beheerde HSM's te beveiligen, zodat alleen gemachtigde toepassingen en gebruikers toegang hebben tot uw beheerde HSM's. Dit artikel bevat een overzicht van het toegangsbeheermodel voor beheerde HSM's. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw beheerde HSM's kunt beveiligen.

## <a name="access-control-model"></a>Toegangsbeheermodel

De toegang tot een beheerde HSM wordt beheerd via twee interfaces: het **beheervlak** en het **gegevensvlak**. In het beheervlak beheert u de HSM zelf. Bewerkingen in dit vlak omvatten het maken en verwijderen van beheerde HSM's en het ophalen van beheerde HSM-eigenschappen. In het gegevensvlak werkt u met de gegevens die zijn opgeslagen in een beheerde HSM. Dit zijn versleutelingssleutels met HSM-back-end. U kunt sleutels toevoegen, verwijderen, wijzigen en gebruiken om cryptografische bewerkingen uit te voeren, roltoewijzingen te beheren om toegang tot de sleutels te beheren, een volledige HSM-back-up te maken, een volledige back-up te herstellen en het beveiligingsdomein te beheren vanuit de interface van het gegevensvlak.

Voor toegang tot een beheerde HSM in beide vlak moeten alle aanroepers de juiste verificatie en autorisatie hebben. Met verificatie wordt de identiteit van de aanroeper vastgesteld. Autorisatie bepaalt welke bewerkingen de aanroeper kan uitvoeren. Een aanroeper kan een van de [beveiligings-principals](../../role-based-access-control/overview.md#security-principal) zijn die zijn gedefinieerd in Azure Active Directory: gebruiker, groep, service-principal of beheerde identiteit.

Beide vlakken gebruiken Azure Active Directory voor verificatie. Voor autorisatie gebruiken ze verschillende systemen als volgt
- Het beheervlak maakt gebruik van op rollen gebaseerd toegangsbeheer van Azure , Azure RBAC, een autorisatiesysteem dat is gebouwd op Azure Azure Resource Manager 
- Het gegevensvlak maakt gebruik van een beheerd RBAC op HSM-niveau (lokale RBAC voor beheerde HSM), een autorisatiesysteem dat wordt geïmplementeerd en afgedwongen op het niveau van de beheerde HSM.

Wanneer een beheerde HSM wordt gemaakt, biedt de requestor ook een lijst met gegevensvlakbeheerders (alle [beveiligingsprincipads](../../role-based-access-control/overview.md#security-principal) worden ondersteund). Alleen deze beheerders hebben toegang tot het gegevensvlak van de beheerde HSM om belangrijke bewerkingen uit te voeren en roltoewijzingen voor gegevensvlakken te beheren (lokale RBAC van beheerde HSM).

Het machtigingsmodel voor beide vlakken gebruikt dezelfde syntaxis, maar ze worden afgedwongen op verschillende niveaus en roltoewijzingen gebruiken verschillende bereiken. Azure RBAC voor beheervlak wordt afgedwongen door Azure Resource Manager terwijl de lokale RBAC van de beheerde HSM voor gegevensvlak wordt afgedwongen door de beheerde HSM zelf.

> [!IMPORTANT]
> Als een beveiligingsprincipale beheervlak toegang wordt gegeven tot een beheerde HSM, krijgen ze geen toegang tot de gegevensvlak voor toegang tot sleutels of roltoewijzingen van gegevensvlakken lokale RBAC van de beheerde HSM). Deze isolatie is ontworpen om onbedoelde uitbreiding van bevoegdheden te voorkomen die van invloed zijn op de toegang tot sleutels die zijn opgeslagen in beheerde HSM.

Een abonnementsbeheerder (omdat deze bijvoorbeeld de machtiging 'Inzender' heeft voor alle resources in het abonnement) kan een beheerde HSM in zijn abonnement verwijderen, maar als deze geen toegang tot de gegevensvlak heeft die specifiek is verleend via de lokale RBAC van de beheerde HSM, kan deze geen toegang krijgen tot sleutels of roltoewijzingen beheren in de beheerde HSM om zichzelf of anderen toegang te verlenen tot de gegevensvlak.

## <a name="azure-active-directory-authentication"></a>Verificatie via Azure Active Directory

Wanneer u een beheerde HSM in een Azure-abonnement maakt, wordt deze automatisch gekoppeld aan de Azure Active Directory tenant van het abonnement. Alle aanroepers op beide vlakken moeten worden geregistreerd in deze tenant en worden geverifieerd voor toegang tot de beheerde HSM.

De toepassing wordt geverifieerd met Azure Active Directory voordat een van beide vlak wordt aanroepen. De toepassing kan elke [ondersteunde verificatiemethode gebruiken op](../../active-directory/develop/authentication-vs-authorization.md) basis van het toepassingstype. De toepassing verkrijgt een token voor een resource in het vlak om toegang te krijgen. De resource is een eindpunt in het beheer- of gegevensvlak, op basis van de Azure-omgeving. De toepassing gebruikt het token en verzendt een REST API naar het beheerde HSM-eindpunt. Bekijk de volledige verificatiestroom [voor meer informatie.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

Het gebruik van één verificatiemechanisme voor beide vlakken heeft verschillende voordelen:

- Organisaties kunnen de toegang tot alle beheerde HMS's in hun organisatie centraal controleren.
- Als een gebruiker weggaat, heeft deze onmiddellijk geen toegang meer tot alle beheerde HMS's in de organisatie.
- Organisaties kunnen verificatie aanpassen met behulp van de opties in Azure Active Directory, zoals meervoudige verificatie inschakelen voor extra beveiliging.

## <a name="resource-endpoints"></a>Resource-eindpunten

Beveiligingsprincipa hebben toegang tot de vlakken via eindpunten. De toegangsregelaars voor de twee vlakken werken onafhankelijk van elkaar. Als u een toepassing toegang wilt verlenen tot het gebruik van sleutels in een beheerde HSM, verleent u toegang tot de gegevensvlak met behulp van de lokale RBAC van de beheerde HSM. Als u een gebruiker toegang wilt verlenen tot een beheerde HSM-resource om de beheerde HSM's te maken, te lezen, te verwijderen, te verplaatsen en andere eigenschappen en tags te bewerken, gebruikt u Azure RBAC.

In de volgende tabel ziet u de eindpunten voor de beheer- en gegevensvlakken.

| &nbsp;Toegangsvlak | Eindpunten voor toegang | Operations | Mechanisme voor toegangsbeheer |
| --- | --- | --- | --- |
| Beheerlaag | **Wereldwijd:**<br> management.azure.com:443<br> | Beheerde HMS's maken, lezen, bijwerken, verwijderen en verplaatsen<br>Beheerde HSM-tags instellen | Azure RBAC |
| Gegevenslaag | **Wereldwijd:**<br> &lt;hsm-name &gt; .managedhsm.azure.net:443<br> | **Sleutels:** ontsleutelen, versleutelen,<br> uitpakken, verpakken, controleren, ondertekenen, krijgen, lijst, bijwerken, maken, importeren, verwijderen, back-up maken, herstellen, opspen<br/><br/> Rolbeheer van gegevensvlak **(lokale RBAC** van beheerde HSM) : roldefinities weergeven, rollen toewijzen, roltoewijzingen _<br/> <br/> verwijderen,_ aangepaste rollen definiëren *Back-up/herstel **: back-up, herstellen, status controleren back-up/herstelbewerkingen <br/> <br/>** Beveiligingsdomein**: beveiligingsdomein downloaden en uploaden | Lokale RBAC van beheerde HSM |
|||||

## <a name="management-plane-and-azure-rbac"></a>Beheervlak en Azure RBAC

In het beheervlak gebruikt u Azure RBAC om de bewerkingen te autoreren die een aanroeper kan uitvoeren. In het Azure RBAC-model heeft elk Azure-abonnement een exemplaar van Azure Active Directory. U verleent vanuit deze directory toegang aan gebruikers, groepen en toepassingen. Toegang wordt verleend voor het beheren van resources in het Azure-abonnement die gebruikmaken van Azure Resource Manager implementatiemodel. Als u toegang wilt verlenen, gebruikt [u de Azure Portal,](https://portal.azure.com/)de [Azure CLI](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs)of de Azure Resource Manager [REST API's.](/rest/api/authorization/roleassignments)

U maakt een sleutelkluis in een resourcegroep en beheert de toegang met behulp van Azure Active Directory. U verleent gebruikers of groepen de mogelijkheid om de sleutelkluizen in een resourcegroep te beheren. U verleent toegang op een specifiek bereikniveau door de juiste Azure-rollen toe te wijzen. Als u een gebruiker toegang wilt verlenen om sleutelkluizen te beheren, wijst u een vooraf gedefinieerde rol toe aan de gebruiker `key vault Contributor` voor een specifiek bereik. De volgende bereiken kunnen worden toegewezen aan een Azure-rol:

- **Beheergroep:** een Azure-rol die is toegewezen op abonnementsniveau is van toepassing op alle abonnementen in die beheergroep.
- **Abonnement:** een Azure-rol die is toegewezen op abonnementsniveau, is van toepassing op alle resourcegroepen en resources binnen dat abonnement.
- **Resourcegroep:** een Azure-rol die is toegewezen op het niveau van de resourcegroep, is van toepassing op alle resources in die resourcegroep.
- **Specifieke resource:** een Azure-rol die is toegewezen voor een specifieke resource, is van toepassing op die resource. In dit geval is de resource een specifieke sleutelkluis.

Er zijn verschillende vooraf gedefinieerde rollen. Als een vooraf gedefinieerde rol niet aan uw behoeften past, kunt u uw eigen rol definiëren. Zie Azure [RBAC: Ingebouwde rollen voor meer informatie.](../../role-based-access-control/built-in-roles.md)

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Lokaal RBAC voor gegevensvlak en beheerde HSM

U verleent een beveiligingsprincipaal toegang om specifieke sleutelbewerkingen uit te voeren door een rol toe te wijzen. Voor elke roltoewijzing moet u een rol en bereik opgeven waarop die toewijzing van toepassing is. Voor lokale RBAC van beheerde HSM zijn twee bereiken beschikbaar.

- **"/" of "/keys"**: bereik op HSM-niveau. Beveiligings-principals die aan dit bereik een rol zijn toegewezen, kunnen de bewerkingen uitvoeren die zijn gedefinieerd in de rol voor alle objecten (sleutels) in de beheerde HSM.
- **"/keys/ &lt; key-name &gt; "**: bereik op sleutelniveau. Beveiligings-principals die aan dit bereik een rol zijn toegewezen, kunnen alleen de bewerkingen uitvoeren die in deze rol zijn gedefinieerd voor alle versies van de opgegeven sleutel.

## <a name="next-steps"></a>Volgende stappen

- Zie Wat is beheerde [HSM?](overview.md) voor een aan de slag-zelfstudie voor een beheerder.
- Zie Lokale [RBAC voor beheerde HSM voor een zelfstudie over rolbeheer](role-management.md)
- Zie Logboekregistratie van beheerde HSM voor meer informatie over logboekregistratie van het gebruik [van beheerde HSM's](logging.md)