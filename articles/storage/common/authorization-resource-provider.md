---
title: De resource provider van Azure Storage gebruiken om toegang te krijgen tot beheer resources
description: De resource provider Azure Storage is een service die toegang biedt tot beheer resources voor Azure Storage. U kunt de resource provider van Azure Storage gebruiken om resources te maken, bij te werken, te beheren en te verwijderen, zoals opslag accounts, persoonlijke eind punten en toegangs sleutels voor accounts.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789672"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>De resource provider van Azure Storage gebruiken om toegang te krijgen tot beheer resources

Azure Resource Manager is de implementatie- en beheersservice voor Azure. De resource provider Azure Storage is een service die is gebaseerd op Azure Resource Manager en die toegang biedt tot beheer resources voor Azure Storage. U kunt de resource provider van Azure Storage gebruiken om resources te maken, bij te werken, te beheren en te verwijderen, zoals opslag accounts, persoonlijke eind punten en toegangs sleutels voor accounts. Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over Azure Resource Manager.

U kunt de Azure Storage Resource provider gebruiken om acties uit te voeren, zoals het maken of verwijderen van een opslag account of het ophalen van een lijst met opslag accounts in een abonnement. Als u aanvragen voor de Azure Storage Resource provider wilt autoriseren, gebruikt u Azure Active Directory (Azure AD). In dit artikel wordt beschreven hoe u machtigingen toewijst aan beheer resources en verwijst naar voor beelden die laten zien hoe u aanvragen voor de resource provider van Azure Storage kunt doen.

## <a name="management-resources-versus-data-resources"></a>Beheer bronnen versus gegevens bronnen

Micro soft biedt twee REST-Api's voor het werken met Azure Storage-resources. Deze Api's vormen de basis van alle acties die u kunt uitvoeren op basis van Azure Storage. Met de Azure Storage REST API kunt u werken met gegevens in uw opslag account, waaronder blob-, wachtrij-, bestands-en tabel gegevens. Met de Azure Storage Resource provider REST API kunt u werken met het opslag account en gerelateerde resources.

Een aanvraag die BLOB-gegevens leest of schrijft, vereist andere machtigingen dan een aanvraag die een beheer bewerking uitvoert. Azure RBAC biedt nauw keurige controle over machtigingen voor beide typen resources. Wanneer u een Azure-rol toewijst aan een beveiligingsprincipal, moet u weten welke machtigingen aan de principal worden verleend. Zie [ingebouwde rollen in azure](../../role-based-access-control/built-in-roles.md)voor een gedetailleerde Naslag informatie over de acties die zijn gekoppeld aan elke ingebouwde rol van Azure.

Azure Storage ondersteunt het gebruik van Azure AD om aanvragen voor Blob-en wachtrij opslag te autoriseren. Zie [toegang tot blobs en wacht rijen toestaan met Active Directory](storage-auth-aad.md)voor meer informatie over Azure-rollen voor Blob-en wachtrij gegevens bewerkingen.

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Beheer machtigingen toewijzen met Azure op rollen gebaseerd toegangs beheer (Azure RBAC)

Elk Azure-abonnement heeft een bijbehorende Azure Active Directory waarmee gebruikers, groepen en toepassingen worden beheerd. Een gebruiker, groep of toepassing wordt ook wel een beveiligingsprincipal genoemd in de context van het [micro soft Identity-platform](../../active-directory/develop/index.yml). U kunt toegang verlenen tot bronnen in een abonnement op een beveiligings-principal die is gedefinieerd in de Active Directory door gebruik te maken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC).

Wanneer u een Azure-rol toewijst aan een beveiligingsprincipal, geeft u ook het bereik op waarop de machtigingen die door de rol worden verleend, van kracht zijn. Voor beheer bewerkingen kunt u een rol toewijzen op het niveau van het abonnement, de resource groep of het opslag account. U kunt een Azure-rol toewijzen aan een beveiligingsprincipal met behulp van de [Azure Portal](https://portal.azure.com/), de [Azure cli-hulpprogram ma's](/cli/azure/install-classic-cli), [Power shell](/powershell/azure/)of de [resource provider van Azure Storage rest API](/rest/api/storagerp).

Zie [Wat is Azure Role-based Access Control (Azure RBAC)?](../../role-based-access-control/overview.md) voor meer informatie. en [klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-beheerders rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

### <a name="built-in-roles-for-management-operations"></a>Ingebouwde rollen voor beheer bewerkingen

Azure biedt ingebouwde rollen die machtigingen verlenen voor het aanroepen van beheer bewerkingen. Azure Storage biedt ook ingebouwde rollen die specifiek zijn voor gebruik met de Azure Storage Resource provider.

Ingebouwde rollen die machtigingen verlenen voor het aanroepen van opslag beheer bewerkingen zijn onder andere de rollen die in de volgende tabel worden beschreven:

|    Azure-rol    |    Beschrijving    |    Inclusief toegang tot account sleutels?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Eigenaar** | Kan alle opslag resources en toegang tot resources beheren.  | Ja, biedt machtigingen voor het weer geven en opnieuw genereren van de sleutel van het opslag account. |
| **Inzender**  | Kan alle opslag resources beheren, maar kan geen toegang tot resources beheren. | Ja, biedt machtigingen voor het weer geven en opnieuw genereren van de sleutel van het opslag account. |
| **Lezer** | Kan informatie over het opslag account weer geven, maar kan de account sleutels niet weer geven. | Nee. |
| **Inzender voor opslagaccounts** | Kan het opslag account beheren, informatie over de resource groepen en-resources van het abonnement ophalen en implementaties van abonnements resource groepen maken en beheren. | Ja, biedt machtigingen voor het weer geven en opnieuw genereren van de sleutel van het opslag account. |
| **Beheerder van gebruikerstoegang** | Kan de toegang tot het opslag account beheren.   | Ja, geeft een beveiligingsprincipal toestemming om machtigingen toe te wijzen aan zichzelf en anderen. |
| **Inzender voor virtuele machines** | Kan virtuele machines beheren, maar niet het opslag account waarmee ze zijn verbonden.   | Ja, biedt machtigingen voor het weer geven en opnieuw genereren van de sleutel van het opslag account. |

De derde kolom in de tabel geeft aan of de ingebouwde rol ondersteuning biedt voor **micro soft. Storage/Storage accounts/listkeys ophalen/Action**. Met deze actie worden machtigingen verleend om de sleutels voor het opslag account te lezen en opnieuw te genereren. Machtigingen voor toegang tot Azure Storage beheer resources bevatten niet ook machtigingen voor toegang tot gegevens. Als een gebruiker echter toegang heeft tot de account sleutels, kunnen ze de account sleutels gebruiken om toegang te krijgen tot Azure Storage gegevens via een gedeelde sleutel autorisatie.

### <a name="custom-roles-for-management-operations"></a>Aangepaste rollen voor beheer bewerkingen

Azure biedt ook ondersteuning voor het definiëren van aangepaste Azure-rollen voor toegang tot beheer resources. Zie [aangepaste rollen voor Azure](../../role-based-access-control/custom-roles.md)voor meer informatie over aangepaste rollen.

## <a name="code-samples"></a>Codevoorbeelden

Zie de volgende voor beelden voor code voorbeelden die laten zien hoe u beheer bewerkingen kunt autoriseren en aanroepen vanuit de Azure Storage-beheer bibliotheken:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager versus klassieke implementaties

Het implementatiemodel van Resource Manager en het klassieke implementatiemodel zijn twee verschillende manieren voor het implementeren en beheren van uw Azure-oplossingen. U wordt aangeraden het Azure Resource Manager-implementatie model te gebruiken wanneer u een nieuw opslag account maakt. Als dat mogelijk is, raadt micro soft u ook aan om bestaande klassieke opslag accounts opnieuw te maken met het Resource Manager-model. Hoewel u een opslag account kunt maken met behulp van het klassieke implementatie model, is het klassieke model minder flexibel en zal het uiteindelijk worden afgeschaft.

Zie [Resource Manager en klassieke implementatie](../../azure-resource-manager/management/deployment-models.md)voor meer informatie over Azure-implementatie modellen.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Wat is Azure RBAC (toegangsbeheer op basis van rollen)?](../../role-based-access-control/overview.md)
- [Schaalbaarheids doelen voor de resource provider van Azure Storage](scalability-targets-resource-provider.md)