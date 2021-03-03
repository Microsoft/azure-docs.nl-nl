---
title: Beheerde identiteiten gebruiken in communicatie Services
titleSuffix: An Azure Communication Services quickstart
description: Met beheerde identiteiten kunt u toegang verlenen tot Azure Communication Services vanuit toepassingen die worden uitgevoerd in azure Vm's, functie-apps en andere resources.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657617"
---
# <a name="use-managed-identities"></a>Beheerde identiteiten gebruiken
Ga aan de slag met Azure Communication Services door beheerde identiteiten te gebruiken. De communicatie Services-identiteits-en SMS-client bibliotheken ondersteunen Azure Active Directory-verificatie (Azure AD) met [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md).

Deze Quick Start laat zien hoe u toegang tot de identiteits-en SMS-client bibliotheken kunt machtigen vanuit een Azure-omgeving die beheerde identiteiten ondersteunt. Ook wordt beschreven hoe u uw code in een ontwikkel omgeving kunt testen.

## <a name="prerequisites"></a>Vereisten

 - Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free)
 - Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Instellen

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Beheerde identiteiten op een virtuele machine of app service inschakelen

Beheerde identiteiten moeten zijn ingeschakeld op de Azure-resources die u wilt autoriseren. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App-Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Azure-rollen toewijzen met de Azure Portal

1. Navigeer naar de Azure Portal.
1. Navigeer naar de Azure Communication Service-Resource.
1. Ga naar Access Control (IAM) menu-> + add-> roltoewijzing toevoegen.
1. Selecteer de rol ' Inzender ' (dit is de enige ondersteunde rol).
1. Selecteer ' door de gebruiker toegewezen beheerde identiteit ' (of een ' door het systeem toegewezen beheerde identiteit ') en selecteer vervolgens de gewenste identiteit. Sla uw selectie op.

![Beheerde identiteits functie](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Azure-rollen toewijzen met Power shell

Als u rollen en machtigingen wilt toewijzen met behulp van Power shell, raadpleegt u [Azure-roltoewijzingen toevoegen of verwijderen met Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end