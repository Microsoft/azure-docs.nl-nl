---
title: Problemen met beveiliging en toegangs beheer oplossen
description: Meer informatie over het oplossen van problemen met beveiligings-en toegangs beheer in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008719"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Problemen met Azure Data Factory beveiliging en toegangs beheer oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor beveiliging en toegangs beheer in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Ongeldig of leeg verificatie sleutel probleem na het uitschakelen van open bare netwerk toegang

#### <a name="symptoms"></a>Symptomen

De zelf-hostende Integration runtime genereert fout ' de verificatie sleutel is ongeldig of leeg ' na het uitschakelen van de open bare netwerk toegang voor Data Factory.

#### <a name="cause"></a>Oorzaak

Het probleem wordt waarschijnlijk veroorzaakt door een probleem met de DNS-omzetting, omdat het uitschakelen van de open bare verbinding en het tot stand brengen van een persoonlijk eind punt geen ondersteuning biedt voor opnieuw verbinding maken.

#### <a name="resolution"></a>Oplossing

1. Is een PsPing naar de FQDN van ADF en is vastgesteld dat de buffer een openbaar eind punt van ADF heeft, zelfs nadat het is uitgeschakeld.

1. Wijzig het hostbestand in VM om een persoonlijk IP-adres toe te wijzen aan FQDN en voer een PsPing opnieuw uit. De buffer kan naar het juiste privé-IP-adres van ADF gaan.

1. Registreer de zelf-hostende Integration runtime opnieuw zodat deze wordt uitgevoerd.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Kan IR-verificatie sleutel niet registreren op zelf-hostende Vm's vanwege een persoonlijke koppeling

#### <a name="symptoms"></a>Symptomen

Kan IR-verificatie sleutel niet registreren op zelf-hostende VM vanwege een persoonlijke koppeling ingeschakeld.

De fout informatie ziet er als volgt uit:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Oorzaak

Het probleem kan worden veroorzaakt door de virtuele machine waarop de SHIR wordt geïnstalleerd. Open bare netwerk toegang moet zijn ingeschakeld om verbinding te maken met de Cloud.

#### <a name="resolution"></a>Oplossing

 **Oplossing 1:** U kunt de onderstaande stappen volgen om het probleem op te lossen:

1. Ga naar de onderstaande koppeling: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Klik op de optie **Probeer het opnieuw** en vul alle vereiste gegevens in. Plak onder eigenschap in de **hoofd tekst** ook:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Klik op **uitvoeren** aan het einde van de pagina om de functie uit te voeren. Zorg ervoor dat u antwoord code 200 ontvangt. De eigenschap die we hebben geplakt, wordt ook weer gegeven in de JSON-definitie.

1. Vervolgens kunt u opnieuw proberen om de IR-verificatie sleutel opnieuw toe te voegen in de Integration runtime.


**Oplossing 2:** Raadpleeg het volgende artikel voor de oplossing:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Probeer de open bare netwerk toegang met de gebruikers interface in te scha kelen.

![Open bare netwerk toegang inschakelen](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Persoonlijke koppeling voor Data Factory](data-factory-private-link.md)
*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)