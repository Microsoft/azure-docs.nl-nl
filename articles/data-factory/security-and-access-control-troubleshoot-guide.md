---
title: Problemen met beveiliging en toegangs beheer oplossen
description: Meer informatie over het oplossen van problemen met beveiligings-en toegangs beheer in Azure Data Factory.
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/24/2021
ms.author: lle
ms.openlocfilehash: fa410441203c50d96c0de1d9188fb73b6fd4d577
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706154"
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

   * [Zelf-hostende IR-poorten en firewalls](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Azure Data Lake Storage-connector](./connector-azure-data-lake-store.md)
  
* Als u een **Azure IR** gebruikt, probeert u de firewall instelling van de gegevens opslag uit te scha kelen. Deze aanpak kan de problemen in de volgende twee situaties oplossen:
  
   * [Azure IR IP-adressen](./azure-integration-runtime-ip-addresses.md) niet in de acceptatie lijst staan.
   * De functie *vertrouwde micro soft-Services toegang geven tot dit opslag account* is uitgeschakeld voor [Azure Blob Storage](./connector-azure-blob-storage.md#supported-capabilities) en [Azure data Lake Storage gen 2](./connector-azure-data-lake-storage.md#supported-capabilities).
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

- Als optie wordt u aangeraden om hand matig een ' Virtual Network-koppeling ' toe te voegen onder de Data Factory ' persoonlijke koppeling DNS-zone '. Raadpleeg de [persoonlijke Azure-koppeling voor Azure Data Factory](./data-factory-private-link.md#dns-changes-for-private-endpoints) artikel voor meer informatie. De instructie is voor het configureren van de privé-DNS-zone of aangepaste DNS-server voor het omzetten van de Data Factory FQDN naar een privé-IP-adres. 

- Als u de privé-DNS-zone of aangepaste DNS-server echter niet wilt configureren, kunt u de volgende tijdelijke oplossing proberen:

  1. Wijzig het hostbestand in Windows en wijs het persoonlijke IP-adres (het Azure Data Factory-particuliere eind punt) toe aan de Azure Data Factory FQDN.
  
     Ga in de Azure VM naar `C:\Windows\System32\drivers\etc` en open het *hostbestand* in Klad blok. Voeg aan het einde van het bestand de regel toe die het privé-IP-adres toewijst aan de FQDN en sla de wijziging op.
     
     ![Scherm afbeelding van het toewijzen van het privé-IP-adres aan de host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Voer dezelfde opdrachten uit als in de voor gaande verificaties om het antwoord te controleren. dit moet het persoonlijke IP-adres bevatten.

  1. Registreer de zelf-hostende Integration runtime opnieuw en het probleem moet worden opgelost.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Kan IR-verificatie sleutel niet registreren op zelf-hostende Vm's vanwege een persoonlijke koppeling

#### <a name="symptoms"></a>Symptomen

U kunt de IR-verificatie sleutel niet registreren op de zelf-hostende VM omdat de persoonlijke koppeling is ingeschakeld. Het volgende fout bericht wordt weer gegeven:

Kan het service token niet ophalen uit de ADF-service met de sleutel * * * * * * * * * * * * * * * * en de tijd is: 0,1250079 seconde, de fout code is: InvalidGatewayKey, activityId is: XXXXXXx en gedetailleerd fout bericht is het IP-adres van de client is geen geldige privé-IP veroorzaakt omdat Data Factory geen toegang kan krijgen tot het open bare netwerk.

#### <a name="cause"></a>Oorzaak

Het probleem kan worden veroorzaakt door de virtuele machine waarin u de zelf-hostende IR wilt installeren. Zorg ervoor dat open bare netwerk toegang is ingeschakeld om verbinding te maken met de Cloud.

#### <a name="resolution"></a>Oplossing

**Oplossing 1**
 
Ga als volgt te werk om het probleem op te lossen:

1. Ga naar de pagina [factoren-bijwerken](/rest/api/datafactory/Factories/Update) .

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

Ga naar de [persoonlijke Azure-koppeling voor Azure Data Factory](./data-factory-private-link.md)om het probleem op te lossen.

Probeer open bare netwerk toegang in te scha kelen op de gebruikers interface, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Scherm afbeelding van het besturings element ' ingeschakeld ' voor ' open bare netwerk toegang toestaan ' in het deel venster netwerk.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

### <a name="adf-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>ADF, persoonlijke DNS-zone onderdrukkingen Azure Resource Manager DNS-omzetting oorzaak fout ' niet gevonden '

#### <a name="cause"></a>Oorzaak
Zowel Azure Resource Manager als ADF gebruiken dezelfde privé zone als een mogelijk conflict op de privé-DNS van de klant met een scenario waarin de Azure Resource Manager records niet worden gevonden.

#### <a name="solution"></a>Oplossing
1. Zoek Privé-DNS-zones **privatelink.Azure.com** in azure Portal.
![Scherm opname van het vinden van Privé-DNS zones.](media/security-access-control-troubleshoot-guide/private-dns-zones.png)
2. Controleer of er een **ADF** van een record is.
![Scherm opname van een record.](media/security-access-control-troubleshoot-guide/a-record.png)
3.  Ga naar **koppelingen naar het virtuele netwerk**, verwijder alle records.
![Scherm opname van virtuele netwerk koppeling.](media/security-access-control-troubleshoot-guide/virtual-network-link.png)
4.  Navigeer naar uw data factory in Azure Portal en maak het persoonlijke eind punt voor Azure Data Factory Portal.
![Scherm opname van het opnieuw maken van een persoonlijk eind punt.](media/security-access-control-troubleshoot-guide/create-private-endpoint.png)
5.  Ga terug naar Privé-DNS zones en controleer of er een nieuwe privé-DNS-zone **privatelink.ADF.Azure.com** is.
![Scherm opname van een nieuwe DNS-record.](media/security-access-control-troubleshoot-guide/check-dns-record.png)

### <a name="connection-error-in-public-endpoint"></a>Verbindings fout in openbaar eind punt

#### <a name="symptoms"></a>Symptomen

Wanneer u gegevens kopieert met de open bare toegang van een Azure Blob Storage-account, mislukt de pijp lijn wille keurig met de volgende fout.

Bijvoorbeeld: de Azure Blob Storage-Sink gebruikt Azure IR (openbaar, niet beheerd VNet) en de Azure SQL Database bron gebruikt de beheerde VNet IR. Of bron/Sink beheerde VNet-IR alleen gebruiken met open bare opslag.

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>Oorzaak

ADF kan nog steeds beheerde VNet-IR gebruiken, maar u kunt wel een dergelijke fout tegen komen, omdat het open bare eind punt naar Azure Blob Storage in Managed VNet niet betrouwbaar is op basis van het test resultaat en Azure Blob Storage en Azure Data Lake Gen2 niet worden ondersteund om via een openbaar eind Virtual Network punt te worden verbonden met het beheerde [virtuele netwerk & beheerde persoonlijke eind punten](https://docs.microsoft.com/azure/data-factory/managed-virtual-network-private-endpoint#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network).

#### <a name="solution"></a>Oplossing

- Er is een persoonlijk eind punt ingeschakeld op de bron en ook aan de Sink-zijde wanneer de beheerde VNet-IR wordt gebruikt.
- Als u het open bare eind punt nog steeds wilt gebruiken, kunt u overschakelen naar open bare IR in plaats van de beheerde VNet-IR te gebruiken voor de bron en de sink. Zelfs als u terugschakelt naar de open bare IR, kan ADF nog steeds gebruikmaken van de beheerde VNet-IR als de beheerde VNet-IR nog steeds is.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Persoonlijke koppeling voor Data Factory](data-factory-private-link.md)
*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Micro soft Q&een pagina](/answers/topics/azure-data-factory.html)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)