---
title: Problemen met Data Management Gateway oplossen
description: Tips voor het oplossen van problemen met betrekking tot Data Management Gateway.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 284486c5db248ced8ada6e7194c7bc5a9be5689f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100388342"
---
# <a name="troubleshoot-issues-with-using-data-management-gateway"></a>Problemen oplossen met behulp van Data Management Gateway
Dit artikel bevat informatie over het oplossen van problemen met het gebruik van Data Management Gateway.

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [zelf-hostende Integration runtime in Data Factory](../create-self-hosted-integration-runtime.md).

Zie het artikel [Data Management Gateway](data-factory-data-management-gateway.md) voor gedetailleerde informatie over de gateway. Zie het artikel [gegevens verplaatsen tussen on-premises en de Cloud](data-factory-move-data-between-onprem-and-cloud.md) voor een overzicht van het verplaatsen van gegevens van een SQL server data base naar Microsoft Azure Blob-opslag met behulp van de gateway.

## <a name="failed-to-install-or-register-gateway"></a>Kan de gateway niet installeren of registreren
### <a name="1-problem"></a>1. probleem
Dit fout bericht wordt weer gegeven bij het installeren en registreren van een gateway, met name tijdens het downloaden van het gateway-installatie bestand.

`Unable to connect to the remote server". Please check your local settings (Error Code: 10003).`

#### <a name="cause"></a>Oorzaak
De computer waarop u de gateway wilt installeren, heeft het meest recente installatie bestand voor de gateway niet gedownload uit het Download centrum vanwege een netwerk probleem.

#### <a name="resolution"></a>Oplossing
Controleer de instellingen van de proxy server voor uw firewall om te zien of de instellingen de netwerk verbinding van de computer naar het [Download centrum](https://download.microsoft.com/)blok keren en werk de instellingen dienovereenkomstig bij.

U kunt ook het installatie bestand voor de nieuwste gateway downloaden van het [Download centrum](https://www.microsoft.com/download/details.aspx?id=39717) op andere computers die toegang hebben tot het Download centrum. U kunt het installatie bestand vervolgens kopiëren naar de gateway-hostcomputer en het hand matig uitvoeren om de gateway te installeren en bij te werken.

### <a name="2-problem"></a>2. probleem
U ziet deze fout wanneer u een gateway probeert te installeren door rechtstreeks op **deze computer** in de Azure Portal op installeren te klikken.

`Error:  Abort installing a new gateway on this computer because this computer has an existing installed gateway and a computer without any installed gateway is required for installing a new gateway.`  

#### <a name="cause"></a>Oorzaak
Er is al een gateway geïnstalleerd op de computer.

#### <a name="resolution"></a>Oplossing
Verwijder de bestaande gateway op de computer en klik op de koppeling **rechtstreeks op deze computer opnieuw installeren** .

### <a name="3-problem"></a>3. probleem
Mogelijk ziet u deze fout bij het registreren van een nieuwe gateway.

`Error: The gateway has encountered an error during registration.`

#### <a name="cause"></a>Oorzaak
Dit bericht kan om een van de volgende redenen worden weer gegeven:

* De indeling van de gateway sleutel is ongeldig.
* De gateway sleutel is ongeldig gemaakt.
* De gateway sleutel is opnieuw gegenereerd vanuit de portal.  

#### <a name="resolution"></a>Oplossing
Controleer of u de juiste gateway sleutel gebruikt vanuit de portal. Genereer, indien nodig, opnieuw een sleutel en gebruik de sleutel om de gateway te registreren.

### <a name="4-problem"></a>4. probleem
Mogelijk wordt het volgende fout bericht weer gegeven wanneer u een gateway registreert.

`Error: The content or format of the gateway key "{gatewayKey}" is invalid, please go to azure portal to create one new gateway or regenerate the gateway key.`



![De inhoud of de indeling van de sleutel is ongeldig](media/data-factory-troubleshoot-gateway-issues/invalid-format-gateway-key.png)

#### <a name="cause"></a>Oorzaak
De inhoud of indeling van de invoer gateway sleutel is onjuist. Een van de redenen hiervan is dat u slechts een deel van de sleutel uit de portal hebt gekopieerd of dat u een ongeldige sleutel gebruikt.

#### <a name="resolution"></a>Oplossing
Genereer een gateway sleutel in de portal en gebruik de Kopieer knop om de volledige sleutel te kopiëren. Plak deze in dit venster om de gateway te registreren.

### <a name="5-problem"></a>5. probleem
Mogelijk wordt het volgende fout bericht weer gegeven wanneer u een gateway registreert.

`Error: The gateway key is invalid or empty. Specify a valid gateway key from the portal.`

![Scherm opname van het fout bericht dat aangeeft dat de gateway sleutel ongeldig of leeg is.](media/data-factory-troubleshoot-gateway-issues/gateway-key-is-invalid-or-empty.png)

#### <a name="cause"></a>Oorzaak
De gateway sleutel is opnieuw gegenereerd of de gateway is verwijderd uit het Azure Portal. Dit kan ook gebeuren als de Data Management Gateway Setup niet het meest recent is.

#### <a name="resolution"></a>Oplossing
Controleer of de Data Management Gateway Setup de meest recente versie is. u kunt de meest recente versie vinden in het micro soft [Download centrum](https://go.microsoft.com/fwlink/p/?LinkId=271260).

Als de installatie actueel/recent en de gateway nog bestaan op de portal, genereert u de gateway sleutel opnieuw in het Azure Portal en gebruikt u de Kopieer knop om de hele sleutel te kopiëren en plakt u deze in dit venster om de gateway te registreren. Als dat niet het geval is, maakt u de gateway opnieuw en begint u opnieuw.

### <a name="6-problem"></a>6. probleem
Mogelijk wordt het volgende fout bericht weer gegeven wanneer u een gateway registreert.

`Error: Gateway has been online for a while, then shows "Gateway is not registered" with the status "Gateway key is invalid"`

![De gateway sleutel is ongeldig of leeg](media/data-factory-troubleshoot-gateway-issues/gateway-not-registered-key-invalid.png)

#### <a name="cause"></a>Oorzaak
Deze fout kan optreden omdat de gateway is verwijderd of de gekoppelde gateway sleutel opnieuw is gegenereerd.

#### <a name="resolution"></a>Oplossing
Als de gateway is verwijderd, maakt u de gateway opnieuw vanuit de portal, klikt u op **registreren**, kopieert u de sleutel uit de portal, plakt u deze en probeert u de gateway te registreren.

Als de gateway nog bestaat, maar de bijbehorende sleutel opnieuw is gegenereerd, gebruikt u de nieuwe sleutel om de gateway te registreren. Als u de sleutel niet hebt, genereert u de sleutel opnieuw vanuit de portal.

### <a name="7-problem"></a>7. probleem
Wanneer u een gateway wilt registreren, moet u mogelijk het pad en het wacht woord voor een certificaat invoeren.

![Scherm afbeelding die laat zien waar u het pad en het wacht woord voor het certificaat opgeeft.](media/data-factory-troubleshoot-gateway-issues/specify-certificate.png)

#### <a name="cause"></a>Oorzaak
De gateway is eerder geregistreerd op andere computers. Tijdens de eerste registratie van een gateway is een versleutelings certificaat gekoppeld aan de gateway. Het certificaat kan op zichzelf worden gegenereerd door de gateway of door de gebruiker worden geleverd.  Dit certificaat wordt gebruikt om referenties van het gegevens archief (gekoppelde service) te versleutelen.  

![Certificaat exporteren](media/data-factory-troubleshoot-gateway-issues/export-certificate.png)

Wanneer u de gateway op een andere hostcomputer herstelt, vraagt de Registratie wizard dit certificaat om referenties die eerder zijn versleuteld met dit certificaat te ontsleutelen.  Zonder dit certificaat kunnen de referenties niet worden ontsleuteld door de nieuwe gateway en zullen de uitvoeringen van de volgende Kopieer activiteit die aan deze nieuwe gateway zijn gekoppeld, mislukken.  

#### <a name="resolution"></a>Oplossing
Als u het referentie certificaat hebt geëxporteerd van de oorspronkelijke gateway computer met behulp van de knop **exporteren** op het tabblad **instellingen** in Data Management Gateway Configuration Manager, gebruikt u het certificaat hier.

U kunt deze fase niet overs Laan wanneer u een gateway herstelt. Als het certificaat ontbreekt, moet u de gateway uit de Portal verwijderen en opnieuw een nieuwe gateway maken.  Werk bovendien alle gekoppelde services die zijn gerelateerd aan de gateway bij door de referenties opnieuw in te voeren.

### <a name="8-problem"></a>8. probleem
Mogelijk wordt het volgende fout bericht weer gegeven.

`Error: The remote server returned an error: (407) Proxy Authentication Required.`

#### <a name="cause"></a>Oorzaak
Deze fout treedt op wanneer uw gateway zich in een omgeving bevindt die een HTTP-proxy nodig heeft om toegang te krijgen tot Internet bronnen, of als het verificatie wachtwoord van uw proxy is gewijzigd, maar deze wordt niet dienovereenkomstig bijgewerkt in uw gateway.

#### <a name="resolution"></a>Oplossing
Volg de instructies in de sectie overwegingen voor proxy server van dit artikel en Configureer proxy-instellingen met Data Management Gateway Configuration Manager.

## <a name="gateway-is-online-with-limited-functionality"></a>De gateway is online met beperkte functionaliteit
### <a name="1-problem"></a>1. probleem
U ziet de status van de gateway als online met beperkte functionaliteit.

#### <a name="cause"></a>Oorzaak
U ziet de status van de gateway als online met beperkte functionaliteit om een van de volgende redenen:

* De gateway kan geen verbinding maken met de Cloud service via Azure Service Bus.
* Cloud service kan via Service Bus geen verbinding maken met de gateway.

Wanneer de gateway online is met beperkte functionaliteit, kunt u de wizard voor het Data Factory kopiëren mogelijk niet gebruiken om gegevens pijplijnen te maken voor het kopiëren van gegevens naar of van on-premises gegevens archieven. Als tijdelijke oplossing kunt u Data Factory editor gebruiken in de portal, Visual Studio of Azure PowerShell.

#### <a name="resolution"></a>Oplossing
De oplossing voor dit probleem (online met beperkte functionaliteit) is gebaseerd op de vraag of de gateway geen verbinding kan maken met de Cloud service of op een andere manier. De volgende secties bieden deze oplossingen.

### <a name="2-problem"></a>2. probleem
U ziet de volgende fout.

`Error: Gateway cannot connect to cloud service through service bus`

![De gateway kan geen verbinding maken met de Cloud service](media/data-factory-troubleshoot-gateway-issues/gateway-cannot-connect-to-cloud-service.png)

#### <a name="cause"></a>Oorzaak
De gateway kan geen verbinding maken met de Cloud service via Service Bus.

#### <a name="resolution"></a>Oplossing
Volg deze stappen om de gateway weer online te krijgen:

1. Uitgaande IP-adres regels op de gateway computer en de bedrijfs firewall toestaan. U kunt IP-adressen vinden in het Windows-gebeurtenis logboek (ID = = 401): er is geprobeerd toegang te krijgen tot een socket op een manier die is verboden door de toegangs machtigingen XX. XX. XX. xx: 9350.
1. Configureer de proxy-instellingen op de gateway. Zie de sectie overwegingen bij de proxy server voor meer informatie.
1. Schakel uitgaande poorten 5671 en 9350-9354 in op zowel de Windows Firewall op de gateway computer als de firewall van het bedrijf. Zie de sectie poorten en Firewall voor meer informatie. Deze stap is optioneel, maar het is raadzaam om de prestaties te verbeteren.

### <a name="3-problem"></a>3. probleem
U ziet de volgende fout.

`Error: Cloud service cannot connect to gateway through service bus.`

#### <a name="cause"></a>Oorzaak
Een tijdelijke fout in de netwerk verbinding.

#### <a name="resolution"></a>Oplossing
Volg deze stappen om de gateway weer online te krijgen:

1. Wacht een paar minuten. de verbinding wordt automatisch hersteld wanneer de fout zich blijft voordoen.
1. Als de fout zich blijft voordoen, start u de Gateway Service opnieuw.

## <a name="failed-to-author-linked-service"></a>Kan geen gekoppelde service maken
### <a name="problem"></a>Probleem
Mogelijk ziet u deze fout wanneer u referentie beheer in de portal wilt gebruiken om referenties in te voeren voor een nieuwe gekoppelde service, of om referenties voor een bestaande gekoppelde service bij te werken.

`Error: The data store '<Server>/<Database>' cannot be reached. Check connection settings for the data source.`

Wanneer u deze fout ziet, kan de pagina instellingen van Data Management Gateway Configuration Manager eruitzien als in de volgende scherm afbeelding.

![Kan de data base niet bereiken](media/data-factory-troubleshoot-gateway-issues/database-cannot-be-reached.png)

#### <a name="cause"></a>Oorzaak
Het TLS/SSL-certificaat is mogelijk verloren gegaan op de gateway computer. De gateway computer kan het certificaat dat momenteel wordt gebruikt voor TLS-versleuteling, niet laden. Mogelijk wordt er een fout bericht in het gebeurtenis logboek weer gegeven dat vergelijkbaar is met het volgende bericht.

 `Unable to get the gateway settings from cloud service. Check the gateway key and the network connection. (Certificate with thumbprint cannot be loaded.)`

#### <a name="resolution"></a>Oplossing
Volg deze stappen om het probleem op te lossen:

1. Start Data Management Gateway Configuration Manager.
2. Schakel over naar het tabblad **Instellingen**.  
3. Klik op de knop **wijzigen** om het TLS/SSL-certificaat te wijzigen.

   ![Knop certificaat wijzigen](media/data-factory-troubleshoot-gateway-issues/change-button-ssl-certificate.png)
4. Selecteer een nieuw certificaat als TLS/SSL-certificaat. U kunt elk TLS/SSL-certificaat gebruiken dat door u of een organisatie wordt gegenereerd.

   ![Certificaat opgeven](media/data-factory-troubleshoot-gateway-issues/specify-http-end-point.png)

## <a name="copy-activity-fails"></a>Kopieer activiteit mislukt
### <a name="problem"></a>Probleem
Mogelijk ziet u de volgende fout ' UserErrorFailedToConnectToSqlserver ' nadat u een pijp lijn in de portal hebt ingesteld.

`Error: Copy activity encountered a user error: ErrorCode=UserErrorFailedToConnectToSqlServer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server`

#### <a name="cause"></a>Oorzaak
Dit kan om verschillende redenen gebeuren en de beperking is dienovereenkomstig afhankelijk.

#### <a name="resolution"></a>Oplossing
Sta uitgaande TCP-verbindingen via TCP/1433 toe aan de Data Management Gateway client voordat u verbinding maakt met een SQL database.

Als de doel database zich in Azure SQL Database bevindt, moet u ook SQL Server firewall instellingen voor Azure controleren.

Raadpleeg de volgende sectie om de verbinding met het on-premises gegevens archief te testen.

## <a name="data-store-connection-or-driver-related-errors"></a>Gegevens opslag of problemen met stuur Programma's
Voer de volgende stappen uit als u verbinding met het gegevens archief of stuur fouten ziet:

1. Start Data Management Gateway Configuration Manager op de gateway computer.
2. Schakel over naar het tabblad **Diagnostische gegevens** .
3. Voeg in **verbinding testen** de waarden van de gateway groep toe.
4. Klik op **testen** om te zien of u verbinding kunt maken met de on-premises gegevens bron vanaf de gateway computer met behulp van de verbindings gegevens en referenties. Als de testverbinding ook niet lukt nadat u een stuurprogramma hebt geïnstalleerd, start u de gateway opnieuw op om de meest recente wijziging op te halen.

![Verbinding testen op het tabblad Diagnostische gegevens](media/data-factory-troubleshoot-gateway-issues/test-connection-in-diagnostics-tab.png)

## <a name="gateway-logs"></a>Gatewaylogboeken
### <a name="send-gateway-logs-to-microsoft"></a>Gateway logboeken naar micro soft verzenden
Wanneer u contact opneemt met Microsoft Ondersteuning om hulp te krijgen bij het oplossen van gateway problemen, wordt u mogelijk gevraagd om uw gateway logboeken te delen. Met de release van de gateway kunt u de vereiste gateway logboeken delen met twee knoppen klikken in Data Management Gateway Configuration Manager.    

1. Ga naar het tabblad **Diagnostische gegevens** in Data Management Gateway Configuration Manager.

    ![Data Management Gateway tabblad Diagnostische gegevens](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-diagnostics-tab.png)
2. Klik op **Logboeken verzenden** om het volgende dialoog venster weer te geven.

    ![Data Management Gateway logboeken verzenden](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-dialog.png)
3. Beschrijving Klik op **Logboeken weer geven** om Logboeken in Logboeken te bekijken.
4. Beschrijving Klik op **Privacy** om de privacyverklaring van micro soft Web Services te bekijken.
5. Wanneer u tevreden bent met wat u gaat uploaden, klikt u op **Logboeken verzenden** om de logboeken van de afgelopen zeven dagen naar micro soft te verzenden voor het oplossen van problemen. U ziet de status van de bewerking voor het verzenden van Logboeken, zoals wordt weer gegeven in de volgende scherm afbeelding.

    ![Scherm opname van de weer gave van de status van de bewerking voor het verzenden van Logboeken.](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-status.png)
6. Nadat de bewerking is voltooid, ziet u een dialoog venster, zoals wordt weer gegeven in de volgende scherm afbeelding.

    ![Data Management Gateway status logboeken verzenden](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-result.png)
7. Sla het **rapport-id** op en deel het met Microsoft ondersteuning. De rapport-ID wordt gebruikt om de gateway logboeken te zoeken die u hebt geüpload voor het oplossen van problemen.  De rapport-ID wordt ook opgeslagen in de logboeken.  U kunt het vinden door de gebeurtenis-ID 25 te bekijken en de datum en tijd te controleren.

    ![Rapport-ID van Logboeken Data Management Gateway verzenden](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-send-logs-report-id.png)    

### <a name="archive-gateway-logs-on-gateway-host-machine"></a>Gateway logboeken archiveren op de hostcomputer van de gateway
Er zijn enkele scenario's waarin u Gateway problemen hebt en u niet rechtstreeks gateway Logboeken kunt delen:

* U installeert de gateway hand matig en registreert de gateway.
* U probeert de gateway te registreren met een opnieuw gegenereerde sleutel in Data Management Gateway Configuration Manager.
* U probeert logboeken te verzenden en de gateway-hostservice kan niet worden verbonden.

Voor deze scenario's kunt u Gateway logboeken opslaan als een zip-bestand en deze delen wanneer u contact opneemt met micro soft ondersteuning. Als er bijvoorbeeld een fout optreedt tijdens het registreren van de gateway, zoals wordt weer gegeven in de volgende scherm afbeelding.   

![Registratie fout Data Management Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-registration-error.png)

Klik op de koppeling **Archief gateway logboeken** om logboeken te archiveren en op te slaan en deel het zip-bestand vervolgens met micro soft ondersteuning.

![Archief logboeken Data Management Gateway](media/data-factory-troubleshoot-gateway-issues/data-management-gateway-archive-logs.png)

### <a name="locate-gateway-logs"></a>Gateway logboeken zoeken
Gedetailleerde informatie over de gateway Logboeken vindt u in de Windows-gebeurtenis Logboeken.

1. Start Windows **Logboeken**.
2. Zoek Logboeken in de **Logboeken van toepassingen en services**  >  **Data Management Gateway** map.

   Wanneer u problemen met de gateway wilt oplossen, zoekt u naar fout niveau gebeurtenissen in de logboeken.

![Data Management Gateway Logboeken in Logboeken](media/data-factory-troubleshoot-gateway-issues/gateway-logs-event-viewer.png)
