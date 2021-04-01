---
title: Azure Block Chain service-beveiliging
description: Azure Block Chain Service Data Access-en Security-concepten
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 989d9f2afad30517a85185878d694c0b6640e987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80879595"
---
# <a name="azure-blockchain-service-security"></a>Azure Block Chain service-beveiliging

De Azure Block Chain-service maakt gebruik van verschillende mogelijkheden van Azure om uw gegevens veilig en beschikbaar te stellen. Gegevens worden beveiligd met isolatie, versleuteling en verificatie.

## <a name="isolation"></a>Isolatie

Azure Block Chain-service bronnen worden geïsoleerd in een particulier virtueel netwerk. Elk trans actie-en validatie knooppunt is een virtuele machine (VM). Vm's in één virtueel netwerk kunnen niet rechtstreeks communiceren met virtuele machines in een ander virtueel netwerk. Isolatie zorgt ervoor dat de communicatie privé blijft binnen het virtuele netwerk. Zie [isolatie in de open bare Azure-Cloud](../../security/fundamentals/isolation-choices.md#networking-isolation)voor meer informatie over de isolatie van het virtuele Azure-netwerk.

![VNET-diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Versleuteling

Gebruikers gegevens worden opgeslagen in azure Storage. Gebruikers gegevens zijn versleuteld in beweging en op rest voor beveiliging en vertrouwelijkheid. Zie [Azure Storage Security Guide (Engelstalig](../../storage/blobs/security-recommendations.md)) voor meer informatie.

## <a name="authentication"></a>Verificatie

Trans acties kunnen via een RPC-eind punt naar Block Chain-knoop punten worden verzonden. Clients communiceren met een transactie knooppunt met behulp van een reverse proxy server die gebruikers verificatie afhandelt en gegevens versleutelt via TLS.

![Verificatie diagram](./media/data-security/authentication.png)

Er zijn drie verificatie methoden voor RPC-toegang.

### <a name="basic-authentication"></a>Basisverificatie

Basis verificatie maakt gebruik van een HTTP-verificatie header met de gebruikers naam en het wacht woord. De gebruikers naam is de naam van het block Chain-knoop punt. Het wacht woord wordt ingesteld tijdens het inrichten van een lid of knoop punt. Het wacht woord kan worden gewijzigd met behulp van de Azure Portal of CLI.

### <a name="access-keys"></a>Toegangssleutels

Toegangs sleutels gebruiken een wille keurig gegenereerde teken reeks die is opgenomen in de URL van het eind punt. Met twee toegangs toetsen kunt u sleutel rotatie inschakelen. Sleutels kunnen opnieuw worden gegenereerd op basis van de Azure Portal en CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) maakt gebruik van een verificatie mechanisme op basis van claims waarbij de gebruiker wordt geverifieerd door Azure AD met behulp van Azure AD-gebruikers referenties. Azure AD biedt identiteits beheer in de Cloud en stelt klanten in staat om één identiteit te gebruiken voor een volledige onderneming en toegang te krijgen tot toepassingen in de Cloud. De Azure Block Chain-service kan worden geïntegreerd met Azure AD Enable ID Federation, eenmalige aanmelding en multi-factor Authentication. U kunt gebruikers, groepen en toepassings rollen toewijzen in uw organisatie voor Block Chain-lidmaatschap en toegang tot knoop punten.

De Azure AD-client proxy is beschikbaar op [github](https://github.com/Microsoft/azure-blockchain-connector/releases). De client proxy stuurt de gebruiker naar de aanmeldings pagina van Azure AD en verkrijgt een Bearer-token bij geslaagde authenticatie. Vervolgens koppelt de gebruiker een Ethereum-client toepassing, zoals Geth of truffle, aan het eind punt van de client proxy. Ten slotte, wanneer een trans actie wordt verzonden, injecteert de client proxy het Bearer-token in de http-header en de omgekeerde proxy valideert het token met behulp van het OAuth-protocol.

## <a name="keys-and-ethereum-accounts"></a>Sleutels en Ethereum-accounts

Bij het inrichten van een Azure Block Chain-service-lid, wordt een Ethereum-account en een openbaar en persoonlijk sleutel paar gegenereerd. De persoonlijke sleutel wordt gebruikt om trans acties naar de Block chain te verzenden. Het Ethereum-account is de laatste 20 bytes van de hash van de open bare sleutel. Het Ethereum-account wordt ook een wallet genoemd.

Het persoonlijke en open bare sleutel paar wordt opgeslagen als keyfile in JSON-indeling. De persoonlijke sleutel wordt versleuteld met het wacht woord dat is ingevoerd bij het maken van de Block Chain-grootboek service.

Persoonlijke sleutels worden gebruikt voor het digitaal ondertekenen van trans acties. In een persoonlijke blockchains vertegenwoordigt een slim contract dat is ondertekend door een persoonlijke sleutel de identiteit van de ondertekenaar. Om de geldigheid van de hand tekening te controleren, kan de ontvanger de open bare sleutel van de ondertekenaar vergelijken met het adres dat is berekend op basis van de hand tekening.

Constellation sleutels worden gebruikt om een quorum knooppunt uniek te identificeren. Constellation-sleutels worden gegenereerd op het moment van het inrichten van het knoop punt en zijn opgegeven in de para meter privateFor van een persoonlijke trans actie in quorum.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory toegang configureren voor de Azure Block Chain-Service](configure-aad.md).
