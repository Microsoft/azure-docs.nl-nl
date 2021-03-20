---
title: Toegang tot Azure Event Hubs autoriseren
description: Dit artikel bevat informatie over verschillende opties voor het machtigen van toegang tot Azure Event Hubs-resources.
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92332379"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Toegang tot Azure Event Hubs autoriseren
Elke keer dat u gebeurtenissen/gegevens van een Event Hub publiceert of gebruikt, probeert de client toegang tot Event Hubs resources te krijgen. Elke aanvraag voor een beveiligde resource moet worden geautoriseerd zodat de service kan controleren of de client over de vereiste machtigingen beschikt om de gegevens te publiceren/te gebruiken. 

Azure Event Hubs biedt de volgende opties voor het machtigen van toegang tot beveiligde resources:

- Azure Active Directory
- Shared Access Signature

> [!NOTE]
> Dit artikel is van toepassing op zowel Event Hubs als [Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) scenario's. 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory-integratie (Azure AD) voor Event Hubs resources biedt op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) voor een nauw keurige controle over de toegang van een client tot resources. U kunt Azure RBAC gebruiken om machtigingen te verlenen aan de beveiligingsprincipal, die een gebruiker, een groep of een Application Service-Principal kan zijn. De beveiligingsprincipal wordt door Azure AD geverifieerd om een OAuth 2,0-token te retour neren. Het token kan worden gebruikt voor het autoriseren van een aanvraag voor toegang tot een Event Hubs bron.

Raadpleeg de volgende artikelen voor meer informatie over verificatie met Azure AD:

- [Aanvragen voor Azure Event Hubs verifiëren met behulp van Azure Active Directory](authenticate-application.md)
- [Toegang tot Event hubs resources toestaan met behulp van Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Shared Access Signatures 
Shared Access signatures (SAS) voor Event Hubs resources bieden beperkte gedelegeerde toegang tot Event Hubs resources. Door beperkingen toe te voegen aan een tijds interval waarvoor de hand tekening geldig is of op machtigingen die door deze worden verleend, biedt het beheer van resources flexibiliteit. Zie [verifiëren met behulp van Shared Access signatures (SAS)](authenticate-shared-access-signature.md)voor meer informatie. 

Het autoriseren van gebruikers of toepassingen met een OAuth 2,0-token dat wordt geretourneerd door Azure AD biedt een superieure beveiliging en gebruiks gemak over Shared Access signatures (SAS). Met Azure AD hoeft u de toegangs tokens niet op te slaan met uw code en mogelijke beveiligings problemen met Risico's. Hoewel u Shared Access signatures (SAS) kunt blijven gebruiken om nauw keurige toegang tot Event Hubs resources te verlenen, biedt Azure AD soort gelijke mogelijkheden zonder dat u SAS-tokens hoeft te beheren, of om u zorgen te maken over het intrekken van een SAS. 

Standaard worden alle Event Hubs-resources beveiligd en zijn ze alleen beschikbaar voor de eigenaar van het account. Hoewel u een van de hierboven beschreven verificatie strategieën kunt gebruiken om clients toegang te verlenen tot Event hub-resources. Micro soft raadt u aan Azure AD zo mogelijk te gebruiken voor maximale beveiliging en gebruiks gemak.

Zie voor meer informatie over autorisatie met behulp van SAS [toegang verlenen tot Event hubs-resources met behulp van hand tekeningen voor gedeelde toegang](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Volgende stappen
- Bekijk de voor [beelden van Azure RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) die zijn gepubliceerd in onze github-opslag plaats. 
- Zie de volgende artikelen:
    - [Aanvragen voor Azure Event Hubs verifiëren vanuit een toepassing met behulp van Azure Active Directory](authenticate-application.md)
    - [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen](authenticate-managed-identity.md)
    - [Aanvragen voor Azure Event Hubs verifiëren met behulp van hand tekeningen voor gedeelde toegang](authenticate-shared-access-signature.md)
    - [Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Toegang tot Event Hubs resources autoriseren met behulp van hand tekeningen voor gedeelde toegang](authorize-access-shared-access-signature.md)

