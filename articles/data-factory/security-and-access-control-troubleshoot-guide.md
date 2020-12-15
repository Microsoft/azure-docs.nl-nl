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
ms.openlocfilehash: 51cb1a1a8151748fc9c6cd4c81da967424b52868
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505151"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Problemen met Azure Data Factory beveiliging en toegangs beheer oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor beveiliging en toegangs beheer in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Connectiviteits probleem in de Kopieer activiteit van het Cloud gegevens archief

#### <a name="symptoms"></a>Symptomen

Er kunnen verschillende fout berichten worden weer gegeven wanneer verbindings problemen optreden in de bron-of sink-gegevens opslag.

#### <a name="cause"></a>Oorzaak 

Het probleem wordt meestal veroorzaakt door een van de volgende factoren:

* De proxy-instelling in het zelf-hostende knoop punt voor Integration runtime (IR), als u een zelf-hostende IR gebruikt.

* De firewall instelling in het zelf-hostende IR-knoop punt als u een zelf-hostende IR gebruikt.

* De firewall instelling in het gegevens archief van de Cloud.

#### <a name="resolution"></a>Oplossing

* Controleer de volgende punten om ervoor te zorgen dat dit een connectiviteits probleem is:

   - De fout wordt gegenereerd op basis van de bron-of sink-connectors.
   - De fout is aan het begin van de Kopieer activiteit.
   - De fout is consistent voor Azure IR of de zelf-hostende IR met één knoop punt, omdat dit een wille keurige fout kan zijn in een zelf-hostende IR met meerdere knoop punten als slechts een deel van het knoop punt het probleem heeft.

* Als u een **zelf-hostende IR** gebruikt, controleert u uw proxy-, firewall-en netwerk instellingen, omdat u verbinding maakt met dezelfde gegevens opslag als u een Azure IR gebruikt. Zie voor het oplossen van dit scenario:

   * [Zelf-hostende IR-poorten en firewalls](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls)
   * [Azure Data Lake Storage-connector](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
* Als u een **Azure IR** gebruikt, probeert u de firewall instelling van de gegevens opslag uit te scha kelen. Deze aanpak kan de problemen in de volgende twee situaties oplossen:
  
   * [Azure IR IP-adressen](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) niet in de acceptatie lijst staan.
   * De functie *vertrouwde micro soft-Services toegang geven tot dit opslag account* is uitgeschakeld voor [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) en [Azure data Lake Storage gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities).
   * De instelling *toegang tot Azure-Services toestaan* is niet ingeschakeld voor Azure data Lake Storage gen1.

Als geen van de voor gaande methoden werkt, neemt u contact op met micro soft voor hulp.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Ongeldig of leeg verificatie sleutel probleem nadat open bare netwerk toegang is uitgeschakeld

#### <a name="symptoms"></a>Symptomen

Nadat u open bare netwerk toegang voor Data Factory hebt uitgeschakeld, wordt door de zelf-hostende Integration runtime de volgende fout gegenereerd: "de verificatie sleutel is ongeldig of leeg."

#### <a name="cause"></a>Oorzaak

Het probleem is waarschijnlijk veroorzaakt door een probleem met het omzetten van een Domain Name System (DNS), omdat het uitschakelen van de open bare verbinding en het tot stand brengen van een persoonlijk eind punt geen verbinding kan maken.

Ga als volgt te werk om te controleren of de Data Factory Fully Qualified Domain Name (FQDN) is omgezet in het open bare IP-adres:

1. Bevestig dat u de virtuele Azure-machine (VM) hebt gemaakt in hetzelfde virtuele netwerk als Data Factory privé-eind punt.

2. Voer PsPing en ping uit vanaf de Azure-VM naar de Data Factory FQDN:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > U moet een poort opgeven voor de PsPing-opdracht. Poort 443 wordt hier weer gegeven, maar is niet vereist.

3. Controleer of beide opdrachten worden omgezet in een Azure Data Factory open bare IP-adres dat is gebaseerd op een opgegeven regio. Het IP-adres moet de volgende indeling hebben: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Oplossing

Ga als volgt te werk om het probleem op te lossen:
- Raadpleeg het artikel [over persoonlijke Azure-koppelingen voor Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints) . De instructie is voor het configureren van de privé-DNS-zone of-server voor het omzetten van de Data Factory FQDN naar een privé-IP-adres.

- We raden u aan om een aangepaste DNS te gebruiken als de lange termijn oplossing. Als u de privé-DNS-zone of-server echter niet wilt configureren, probeert u de volgende tijdelijke oplossing:

  1. Wijzig het hostbestand in Windows en wijs het persoonlijke IP-adres (het Azure Data Factory-particuliere eind punt) toe aan de Azure Data Factory FQDN.
  
     Ga in de Azure VM naar `C:\Windows\System32\drivers\etc` en open het *hostbestand* in Klad blok. Voeg aan het einde van het bestand de regel toe die het privé-IP-adres toewijst aan de FQDN en sla de wijziging op.
     
     ![Scherm afbeelding van het toewijzen van het privé-IP-adres aan de host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Voer dezelfde opdrachten uit als in de voor gaande verificaties om het antwoord te controleren. dit moet het persoonlijke IP-adres bevatten.

  1. Registreer de zelf-hostende Integration runtime opnieuw en het probleem moet worden opgelost.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Kan IR-verificatie sleutel niet registreren op zelf-hostende Vm's vanwege een persoonlijke koppeling

#### <a name="symptoms"></a>Symptomen

U kunt de IR-verificatie sleutel niet registreren op de zelf-hostende VM omdat de persoonlijke koppeling is ingeschakeld. Het volgende fout bericht wordt weer gegeven:

Kan het service token niet ophalen uit de ADF-service met de sleutel * * * * * * * * * * * * * * * * en de tijd is: 0,1250079 seconden, de fout code is: InvalidGatewayKey, activityId is: XXXXXXx en gedetailleerd fout bericht is het IP-adres van de client is geen geldige privé-IP veroorzaakt omdat Data Factory geen toegang kan krijgen tot het open bare netwerk.

#### <a name="cause"></a>Oorzaak

Het probleem kan worden veroorzaakt door de virtuele machine waarin u de zelf-hostende IR wilt installeren. Zorg ervoor dat open bare netwerk toegang is ingeschakeld om verbinding te maken met de Cloud.

#### <a name="resolution"></a>Oplossing

**Oplossing 1**
 
Ga als volgt te werk om het probleem op te lossen:

1. Ga naar de pagina [factoren-bijwerken](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) .

1. Selecteer in de rechter bovenhoek de knop **try it** .
1. Vul de vereiste gegevens onder **para meters** in. 
1. Plak onder **hoofd tekst** de volgende eigenschap:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Selecteer **uitvoeren** om de functie uit te voeren. 

1. Vul de vereiste gegevens onder **para meters** in. 

1. Plak onder **hoofd tekst** de volgende eigenschap:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selecteer **uitvoeren** om de functie uit te voeren. 
1. Bevestig dat de **antwoord code: 200** wordt weer gegeven. De eigenschap die u hebt geplakt, moet ook worden weer gegeven in de JSON-definitie.

1. Voeg de IR-verificatie sleutel opnieuw toe in de Integration runtime.


**Oplossing 2**

Ga naar de [persoonlijke Azure-koppeling voor Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-private-link)om het probleem op te lossen.

Probeer open bare netwerk toegang in te scha kelen op de gebruikers interface, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Scherm afbeelding van het besturings element ' ingeschakeld ' voor ' open bare netwerk toegang toestaan ' in het deel venster netwerk.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Persoonlijke koppeling voor Data Factory](data-factory-private-link.md)
*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Micro soft Q&een pagina](/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
