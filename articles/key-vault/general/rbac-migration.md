---
title: Migreren naar op rollen gebaseerd toegangsbeheer van Azure | Microsoft Docs
description: Meer informatie over het migreren van toegangsbeleid voor kluizen naar Azure-rollen.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: a369ed26ca91dbf951b28b99250c6307608c5eb3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749074"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>Migreren van toegangsbeleid voor de kluis naar een machtigingsmodel voor op rollen gebaseerd toegangsbeheer van Azure

Het toegangsbeleidsmodel voor de kluis is een bestaand autorisatiesysteem dat is ingebouwd in Key Vault om toegang te bieden tot sleutels, geheimen en certificaten. U kunt de toegang controleren door afzonderlijke machtigingen toe te wijzen aan de beveiligingsprincipaal (gebruiker, groep, service-principal, beheerde identiteit) op Key Vault bereik. 

Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) is een autorisatiesysteem dat is gebouwd op [Azure Resource Manager](../../azure-resource-manager/management/overview.md) dat een fijnkeurig toegangsbeheer van Azure-resources biedt. Met Azure RBAC kunt u de toegang tot resources beheren door roltoewijzingen te maken. Deze bestaan uit drie elementen: beveiligingsprincipaal, roldefinitie (vooraf gedefinieerde set machtigingen) en bereik (groep resources of afzonderlijke resource). Zie Op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../../role-based-access-control/overview.md)voor meer informatie.

Voordat u migreert naar Azure RBAC, is het belangrijk om de voordelen en beperkingen ervan te begrijpen.

Belangrijke voordelen van Azure RBAC ten opzichte van toegangsbeleid voor kluizen:
- Biedt geïntegreerd model voor toegangsbeheer voor Azure-resources - dezelfde API voor Azure-services
- Gecentraliseerd toegangsbeheer voor beheerders: alle Azure-resources in één weergave beheren
- Geïntegreerd met [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) voor op tijd gebaseerd toegangsbeheer
- Toewijzingen weigeren: de mogelijkheid om een beveiligingsprincipaal uit te sluiten voor een bepaald bereik. Zie Inzicht in [Azure Deny Assignments (Azure Deny Assignments) voor meer informatie](../../role-based-access-control/deny-assignments.md)

Nadelen van Azure RBAC:
- Latentie voor roltoewijzingen: het kan enkele minuten duren voordat roltoewijzing is toegepast. Toegangsbeleid voor de kluis wordt direct toegewezen.
- Beperkt aantal roltoewijzingen- 2000 roltoewijzingen per abonnement versus 1024 toegangsbeleid per Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Toegangsbeleid voor azure-rollentoewijzing

Azure RBAC heeft verschillende ingebouwde Azure-rollen die u kunt toewijzen aan gebruikers, groepen, service-principals en beheerde identiteiten. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen [aangepaste Azure-rollen](../../role-based-access-control/custom-roles.md) maken.

Key Vault ingebouwde rollen voor toegangsbeheer voor sleutels, certificaten en geheimen:
- Key Vault Administrator
- Key Vault Lezer
- Key Vault Certificate Officer
- Key Vault Crypto Officer
- Key Vault cryptogebruiker
- Key Vault cryptoserviceversleutelingsgebruiker
- Key Vault Secrets Officer
- Key Vault Geheimen-gebruiker

Zie Ingebouwde Azure-rollen voor meer informatie over [bestaande ingebouwde rollen](../../role-based-access-control/built-in-roles.md)

Toegangsbeleid voor kluizen kan worden toegewezen met afzonderlijk geselecteerde machtigingen of met vooraf gedefinieerde machtigingssjablonen.

Vooraf gedefinieerde machtigingssjablonen voor toegangsbeleid:
- Sleutel, geheim, certificaatbeheer
- Key & Secret Management
- Secret & Certificate Management
- Sleutelbeheer
- Geheimbeheer
- Certificaatbeheer
- SQL Server-connector
- Azure Data Lake Storage of Azure Storage
- Azure Backup
- Exchange Online-klantsleutel
- SharePoint Online-klantsleutel
- Azure Information BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Toegangsbeleidssjablonen voor toewijzing van Azure-rollen
| Sjabloon voor toegangsbeleid | Operations | Azure-rol |
| --- | --- | --- |
| Sleutel, geheim, certificaatbeheer | Sleutels: alle bewerkingen <br>Certificaten: alle bewerkingen<br>Geheimen: alle bewerkingen | Key Vault Administrator |
| Sleutelbeheer & geheimen | Sleutels: alle bewerkingen <br>Geheimen: alle bewerkingen| Key Vault Crypto Officer <br> Key Vault Secrets Officer |
| Secret & Certificate Management | Certificaten: alle bewerkingen <br>Geheimen: alle bewerkingen| Key Vault Certificates Officer <br> Key Vault Secrets Officer|
| Sleutelbeheer | Sleutels: alle bewerkingen| Key Vault Crypto Officer|
| Geheimbeheer | Geheimen: alle bewerkingen| Key Vault Secrets Officer|
| Certificaatbeheer | Certificaten: alle bewerkingen | Key Vault Certificates Officer|
| SQL Server-connector | Sleutels: ophalen, lijst, sleutel verpakken, sleutel uitpakken | Key Vault cryptoserviceversleutelingsgebruiker|
| Azure Data Lake Storage of Azure Storage | Sleutels: ophalen, lijst, sleutel uitpakken | N.v.t.<br> Aangepaste rol vereist|
| Azure Backup | Sleutels: ophalen, lijst, back-up<br> Certificaat: get, list, backup | N.v.t.<br> Aangepaste rol vereist|
| Exchange Online-klantsleutel | Sleutels: ophalen, lijst, sleutel verpakken, sleutel uitpakken | Key Vault cryptoserviceversleutelingsgebruiker|
| Exchange Online-klantsleutel | Sleutels: ophalen, lijst, sleutel verpakken, sleutel uitpakken | Key Vault cryptoserviceversleutelingsgebruiker|
| Azure Information BYOK | Sleutels: ophalen, ontsleutelen, ondertekenen | N.v.t.<br>Aangepaste rol vereist|


## <a name="assignment-scopes-mapping"></a>Toewijzing van toewijzingsbereiken  

Met Azure RBAC Key Vault roltoewijzing op de volgende scopes:
- Beheergroep
- Abonnement
- Resourcegroep
- Key Vault resource
- Afzonderlijke sleutel, geheim en certificaat

Het machtigingsmodel voor het toegangsbeleid voor de kluis is beperkt tot het toewijzen van beleid op Key Vault resourceniveau, dat 

Over het algemeen is het best practice één sleutelkluis per toepassing te hebben en toegang te beheren op sleutelkluisniveau. Er zijn scenario's waarin het beheren van toegang op andere bereiken het toegangsbeheer kan vereenvoudigen.

- **Infrastructuur, beveiligingsbeheerders en operators: voor het beheren van een groep sleutelkluizen op beheergroep-, abonnements- of resourcegroepniveau met toegangsbeleid voor de kluis is onderhoud voor elke sleutelkluis vereist. Met Azure RBAC kunt u één roltoewijzing maken in de beheergroep, het abonnement of de resourcegroep. Deze toewijzing is van toepassing op nieuwe sleutelkluizen die onder hetzelfde bereik worden gemaakt. In dit scenario is het raadzaam om een Privileged Identity Management Just-In-Time-toegang te gebruiken via permanente toegang.
 
- **Toepassingen: er zijn scenario's waarin de toepassing een geheim moet delen met andere toepassingen. Er moest een afzonderlijke sleutelkluis worden gemaakt met behulp van kluistoegangs police om te voorkomen dat toegang tot alle geheimen werd verlenen. Met Azure RBAC kunt u een rol toewijzen met een bereik voor afzonderlijke geheimen in plaats daarvan met behulp van één sleutelkluis.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Stappen voor kluistoegangsbeleid voor Azure RBAC-migratie
Er zijn veel verschillen tussen het machtigingsmodel voor Toegangsbeleid voor Azure RBAC en kluistoegang. Om uitval tijdens de migratie te voorkomen, worden de onderstaande stappen aanbevolen.
 
1. **Rollen identificeren en toewijzen:** identificeer ingebouwde rollen op basis van bovenstaande toewijzingstabel en maak aangepaste rollen wanneer dat nodig is. Wijs rollen toe aan scopes, op basis van richtlijnen voor het toewijzen van scopes. Zie Toegang bieden tot Key Vault met op rollen gebaseerd toegangsbeheer van Azure voor meer informatie over het toewijzen van rollen [aan een sleutelkluis](rbac-guide.md)
1. **Roltoewijzing valideren:** het kan enkele minuten duren voordat roltoewijzingen in Azure RBAC zijn doorgegeven. Zie Lijst met roltoewijzingen op bereik voor informatie over het controleren [van roltoewijzingen](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **Bewaking en waarschuwingen configureren voor key vault:** het is belangrijk om logboekregistratie in teschakelen en waarschuwingen in te stellen voor uitzonderingen met geweigerde toegang. Zie Bewaking en waarschuwingen [voor Azure Key Vault](./alert.md)
1. **Het machtigingsmodel voor** op rollen gebaseerd toegangsbeheer van Azure instellen op Key Vault: als u het Azure RBAC-machtigingsmodel inschakelen, worden al het bestaande toegangsbeleid ongeldig. Als er een fout is opgetreden, kan het machtigingsmodel worden teruggeschakeld terwijl alle bestaande toegangsbeleidsregels ongewijzigd blijven.

> [!NOTE]
> Voor het wijzigen van het machtigingsmodel is de machtiging Microsoft.Authorization/roleAssignments/write vereist, die deel uitmaakt van de rollen Eigenaar en [Gebruikerstoegangbeheerder.](../../role-based-access-control/built-in-roles.md#user-access-administrator) [](../../role-based-access-control/built-in-roles.md#owner) Klassieke abonnementsbeheerdersrollen zoals 'Servicebeheerder' en 'Medebeheerder' worden niet ondersteund.

> [!NOTE]
> Wanneer het Azure RBAC-machtigingsmodel is ingeschakeld, mislukken alle scripts die proberen toegangsbeleid bij te werken. Het is belangrijk om deze scripts bij te werken voor het gebruik van Azure RBAC.

## <a name="troubleshooting"></a>Problemen oplossen
-  Roltoewijzing werkt na enkele minuten niet. Er zijn situaties waarin roltoewijzingen langer kunnen duren. Het is belangrijk om logica voor opnieuw proberen in code te schrijven om deze gevallen te behandelen.
- Roltoewijzingen zijn verdwenen toen Key Vault werd verwijderd (voorlopig verwijderen) en hersteld. Dit is momenteel een beperking van de functie voor voorlopig verwijderen voor alle Azure-services. U moet alle roltoewijzingen na het herstel opnieuw maken.    

## <a name="learn-more"></a>Lees meer

- [Overzicht van Azure RBAC](../../role-based-access-control/overview.md)
- [Zelfstudie aangepaste rollen](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)