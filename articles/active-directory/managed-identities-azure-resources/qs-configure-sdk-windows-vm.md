---
title: Een SDK gebruiken om beheerde identiteiten te configureren in een VM - Azure AD
description: Stapsgewijze instructies voor het configureren en gebruiken van beheerde identiteiten voor Azure-resources op een virtuele Azure-machine, met behulp van een Azure SDK.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d1be35d2add24cbbc171c3da3bd4d06bce4337
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359660"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Een VM configureren met beheerde identiteiten voor Azure-resources met een Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory (AD). U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel vindt u informatie over het inschakelen en verwijderen van beheerde identiteiten voor Azure-resources voor een virtuele Azure-machine met behulp van een Azure SDK.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDK's met beheerde identiteiten voor Azure-resourcesondersteuning 

Azure ondersteunt meerdere programmeerplatforms via een reeks [Azure SDK's](https://azure.microsoft.com/downloads). Verschillende SDK's zijn bijgewerkt om beheerde identiteiten voor Azure-resources te ondersteunen en beschikken over bijbehorende voorbeelden om het gebruik te demonstreren. Deze lijst wordt bijgewerkt als er aanvullende ondersteuning wordt toegevoegd:

| SDK | Voorbeeld |
| --- | ------ | 
| .NET   | [Resource beheren vanaf een VM ingeschakeld met beheerde identiteiten voor Azure-resources ingeschakeld](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Opslag beheren vanaf een VM ingeschakeld met beheerde identiteiten voor Azure-resources](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Een VM maken met door het systeem toegewezen identiteit ingeschakeld](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Een VM maken met door het systeem toegewezen identiteit ingeschakeld](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Een Azure VM maken met een door het systeem toegewezen identiteit ingeschakeld](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Volgende stappen

- Zie verwante artikelen onder **Identiteit configureren voor een virtuele Azure-machine** voor meer informatie over hoe u ook Azure Portal, PowerShell, CLI en resourcesjablonen kunt gebruiken.
