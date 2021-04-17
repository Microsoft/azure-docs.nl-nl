---
title: 'Azure Digital Twins aanvraag is mislukt met status: 404 Sub-Domain niet gevonden'
description: "Oorzaken en oplossingen voor 'Serviceaanvraag is mislukt. Status: 404 Sub-Domain niet gevonden' op Azure Digital Twins."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/13/2021
ms.openlocfilehash: e3fe3ad22098d796faa309ff3509c318a7e1df4d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590247"
---
# <a name="service-request-failed-status-404-sub-domain-not-found"></a>Serviceaanvraag is mislukt. Status: 404 Sub-Domain niet gevonden

In dit artikel worden de oorzaken en oplossingsstappen beschreven voor het ontvangen van een 404-fout van serviceaanvragen naar Azure Digital Twins. 

## <a name="symptoms"></a>Symptomen

Deze fout kan optreden bij het openen van een Azure Digital Twins-exemplaar met behulp van een service-principal of gebruikersaccount dat tot een andere [Azure Active Directory-tenant (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) van het exemplaar behoort. De juiste [rollen](concepts-security.md) lijken te zijn toegewezen aan de identiteit, maar API-aanvragen mislukken met de foutstatus `404 Sub-Domain not found` .

## <a name="causes"></a>Oorzaken

### <a name="cause-1"></a>Oorzaak #1

Azure Digital Twins vereist dat alle gebruikers die zich authenticeren, deel uitmaken van dezelfde Azure AD-tenant als de Azure Digital Twins-instantie.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

## <a name="solutions"></a>Oplossingen

### <a name="solution-1"></a>Oplossings #1

U kunt dit probleem oplossen door elke federatief identiteit van een andere tenant een **token** te laten aanvragen bij de 'home'-tenant van het Azure Digital Twins-exemplaar. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

### <a name="solution-2"></a>Oplossingsoplossing #2

Als u de klasse in uw code gebruikt en u dit probleem blijft ondervinden nadat u een token hebt ontvangen, kunt u de basisten tenant opgeven in de opties om de tenant te verduidelijken, zelfs wanneer verificatie standaard is ingesteld op een ander `DefaultAzureCredential` `DefaultAzureCredential` type.

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beveiliging en machtigingen voor Azure Digital Twins:
* [*Concepten: Beveiliging voor Azure Digital Twins oplossingen*](concepts-security.md)
