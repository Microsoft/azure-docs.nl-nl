---
title: Wat is OPC Vault - Azure | Microsoft Docs
description: In dit artikel vindt u een overzicht van OPC Vault. Het kan de levensduur van de certificaten voor OPC UA-toepassingen in de cloud configureren, registreren en beheren.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646706"
---
# <a name="what-is-opc-vault"></a>Wat is OPC Vault?

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

OPC Vault is een microservice die de levenscyclus van certificaten voor OPC UA-server en clienttoepassingen in de cloud kan configureren, registreren en beheren. In dit artikel worden de eenvoudige gebruiksvoorbeelden van OPC Vault beschreven.

## <a name="certificate-management"></a>Certificaatbeheer

Bijvoorbeeld een productiebedrijf dat zijn OPC UA-servermachine moet verbinden met een nieuwe clienttoepassingen. Wanneer de fabrikant de eerste keer de servermachine probeert te openen, wordt een foutmelding weergegeven op de OPC UA-servertoepassing om aan te geven dat de clienttoepassing niet veilig is. Dit mechanisme is gebaseerd op de OPC UA-servermachine om te voorkomen dat onbevoegde toepassingen toegang krijgen, om te voorkomen dat de winkel gehackt wordt.

## <a name="application-security-management"></a>Beveiligingsbeheer van de toepassing
Een beveiligingsprofessional gebruikt de OPC Vault-microservice om de OPC UA-server eenvoudig in staat te stellen om te communiceren met de clienttoepassing, aangezien OPC Vault alle functies heeft voor registratie, opslag en levenscyclusbeheer van certificaten. De OPC UA-server is nu veilig verbonden en kan communiceren met de zopas gecreëerde clienttoepassing

## <a name="the-complete-opc-vault-architecture"></a>De volledige OPC Vault-architectuur
In het volgende diagram ziet u de volledige OPC Vault-architectuur.

![OPC Vault-architectuur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Volgende stappen

Nu u over OPC Vault en het gebruik ervan hebt geleerd, is dit de voorgestelde volgende stap:

[OPC Vault-architectuur](overview-opc-vault-architecture.md)