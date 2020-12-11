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
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109743"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Problemen met Azure Data Factory beveiliging en toegangs beheer oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor beveiliging en toegangs beheer in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>Connectiviteits probleem van Kopieer activiteit in gegevens archief in de Cloud

#### <a name="symptoms"></a>Symptomen

Er kunnen verschillende soorten fout berichten worden weer gegeven wanneer er een connectiviteits probleem is opgetreden voor het bron/Sink-gegevens archief.

#### <a name="cause"></a>Oorzaak 

Het probleem wordt meestal veroorzaakt door de volgende factoren:

1. Proxy-instelling in het zelf-hostende IR-knoop punt (als u gebruikmaakt van zelf-hostende IR)

1. Firewall instelling in het zelf-hostende IR-knoop punt (als u gebruikmaakt van zelf-hostende IR)

1. Firewall instelling in gegevens archief in de Cloud

#### <a name="resolution"></a>Oplossing

1. Controleer eerst de volgende punten om er zeker van te zijn dat het probleem wordt veroorzaakt door connectiviteits probleem:

   - De fout wordt gegenereerd op basis van de bron-Sink-connectors.

   - De activiteit mislukt aan het begin van de kopie

   - Het is een consistente fout voor Azure IR of zelf-hostende IR met één knoop punt, omdat dit een wille keurige fout kan zijn voor zelf-hostende IR met meerdere knoop punten als slechts een deel van de knoop punten het probleem heeft.

1. Controleer uw proxy-, firewall-en netwerk instellingen als u een **zelf-hostende IR** gebruikt terwijl de uitvoering naar hetzelfde gegevens archief in azure IR kan slagen. Raadpleeg de volgende koppelingen voor probleem oplossing:

   [Zelf-hostende IR-poorten en firewalls](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    [ADLS-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Als u **Azure IR** gebruikt, schakelt u de firewall instelling van gegevens opslag uit. Op deze manier kan het probleem voor de volgende twee omstandigheden worden opgelost:
  
   * [Azure IR IP-adressen](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) niet zijn toegestaan in de lijst.

   * *Vertrouwde micro soft-Services toegang geven tot dit opslag account* is uitgeschakeld voor [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) en [ADLS gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities).

   * *Toegang tot Azure-Services toestaan* is niet ingeschakeld voor ADLS gen1.

1. Als de bovenstaande methoden niet werken, neemt u contact op met micro soft voor hulp.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Ongeldig of leeg verificatie sleutel probleem nadat open bare netwerk toegang is uitgeschakeld

#### <a name="symptoms"></a>Symptomen

Nadat de open bare netwerk toegang voor Data Factory is uitgeschakeld, omdat de zelf-hostende Integration runtime de volgende fout genereert: de verificatie sleutel is ongeldig of leeg.

#### <a name="cause"></a>Oorzaak

Het probleem wordt waarschijnlijk veroorzaakt door een probleem met de DNS-omzetting, omdat het uitschakelen van de open bare verbinding en het tot stand brengen van een persoonlijk eind punt geen ondersteuning biedt voor opnieuw verbinding maken.

U kunt de onderstaande stappen volgen om te controleren of Data Factory FQDN wordt omgezet naar een openbaar IP-adres:

1. Bevestig dat u de Azure-VM in hetzelfde VNET hebt gemaakt als Data Factory privé-eind punt.

2. Voer PsPing en ping uit van Azure VM naar Data Factory FQDN:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > Er moet een poort worden opgegeven voor de PsPing-opdracht, terwijl de 443-poort niet vereist is.

3. Controleer of beide opdrachten zijn omgezet in een openbaar IP-adres voor ADF dat is gebaseerd op de opgegeven regio (Format xxx. xxx. xxx. 0).

#### <a name="resolution"></a>Oplossing

- U kunt verwijzen naar het artikel in de [persoonlijke Azure-koppeling voor Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints). De instructie is voor het configureren van de privé-DNS-zone/-Server voor het omzetten van Data Factory FQDN naar een privé-IP-adres.

- Als u de privé-DNS-zone/-server momenteel niet wilt configureren, voert u de stappen uit die worden beschreven als tijdelijke oplossing. Aangepaste DNS wordt echter nog steeds aanbevolen voor een lange termijn oplossing.

  1. Wijzig het hostbestand in Windows en wijs persoonlijk IP-adres (ADF persoonlijk eind punt) toe aan de FQDN van ADF.
  
     Navigeer naar pad "C:\Windows\System32\drivers\etc" in azure VM en open het **hostbestand** met Klad blok. Voeg aan het einde van het bestand de regel voor het toewijzen van een privé-IP-adres toe aan FQDN en sla de wijziging op.
     
     ![Toewijzing toevoegen aan host](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Voer dezelfde opdrachten in de bovenstaande verificatie stappen uit om het antwoord te controleren. dit moet het privé-IP-adres bevatten.

  1. Registreer de zelf-hostende Integration runtime opnieuw en het probleem moet worden opgelost.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Kan IR-verificatie sleutel niet registreren op zelf-hostende Vm's vanwege een persoonlijke koppeling

#### <a name="symptoms"></a>Symptomen

Kan IR-verificatie sleutel niet registreren op zelf-hostende VM vanwege een persoonlijke koppeling ingeschakeld.

De fout informatie ziet er als volgt uit:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Oorzaak

Het probleem kan worden veroorzaakt door de VM waarin u de zelf-hostende IR wilt installeren. Als u verbinding wilt maken met de Cloud, moet u ervoor zorgen dat open bare netwerk toegang is ingeschakeld.

#### <a name="resolution"></a>Oplossing

 **Oplossing 1:** U kunt de onderstaande stappen volgen om het probleem op te lossen:

1. Ga naar de pagina [factoren-bijwerken](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) .

1. Selecteer in de rechter bovenhoek de knop **try it** .

1. Vul de vereiste gegevens onder **para meters** in. 

1. Plak onder **hoofd tekst** de volgende eigenschap:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selecteer **uitvoeren** om de functie uit te voeren. 

1. Bevestig dat de **antwoord code: 200** wordt weer gegeven. De eigenschap die u hebt geplakt, moet ook worden weer gegeven in de JSON-definitie.

1. Voeg de IR-verificatie sleutel opnieuw toe in de Integration runtime.


**Oplossing 2:** Raadpleeg het volgende artikel voor de oplossing:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Probeer open bare netwerk toegang in te scha kelen op de gebruikers interface, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Open bare netwerk toegang inschakelen](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Persoonlijke koppeling voor Data Factory](data-factory-private-link.md)
*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Micro soft Q&een pagina](/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
