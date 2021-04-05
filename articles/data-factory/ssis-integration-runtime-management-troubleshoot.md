---
title: Problemen met SSIS Integration Runtime Management oplossen
description: Dit artikel bevat richt lijnen voor probleem oplossing voor beheer problemen van SSIS Integration Runtime (SSIS IR)
ms.service: data-factory
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 917bcce641cfd5178f868e4338354edb78062ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361839"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Problemen met SSIS Integration Runtime Management in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dit artikel bevat richt lijnen voor probleem oplossing voor beheer problemen in Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), ook wel SSIS IR genoemd.

## <a name="overview"></a>Overzicht

Als u een probleem ondervindt bij het inrichten of ongedaan maken van de SSIS-IR, ziet u een fout bericht in de Microsoft Azure Data Factory portal of een fout die is geretourneerd door een Power shell-cmdlet. De fout wordt altijd weer gegeven in de indeling van een fout code met een gedetailleerd fout bericht.

Als de fout code InternalServerError is, heeft de service tijdelijke problemen. Voer de bewerking later opnieuw uit. Als een nieuwe poging niet helpt, neemt u contact op met het ondersteunings team van Azure Data Factory.

Anders kunnen drie belang rijke externe afhankelijkheden fouten veroorzaken: Azure SQL Database of Azure SQL Managed instance, een aangepast installatie script en een configuratie van een virtueel netwerk.

## <a name="sql-database-or-sql-managed-instance-issues"></a>Problemen met SQL Database of SQL Managed instance

SQL Database of SQL Managed instance is vereist als u SSIS IR inricht met een SSIS-catalogus database. De SSIS IR moet toegang kunnen krijgen tot SQL Database of een door SQL beheerd exemplaar. Daarnaast moet het aanmeldings account voor de SQL Database of het SQL Managed instance zijn gemachtigd om een SSIS-catalogus database (SSISDB) te maken. Als er een fout optreedt, wordt een foutcode met een gedetailleerd SQL-uitzonderingsbericht weergegeven in de Data Factory-portal. Gebruik de informatie in de volgende lijst om de problemen met de foutcodes op te lossen.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

U kunt dit probleem tegenkomen wanneer u een nieuwe SSIS IR inricht of wanneer IR wordt uitgevoerd. Als u deze fout tijdens het inrichten van IR ondervindt, kunt u een gedetailleerd SqlException-bericht ontvangen in het foutbericht dat op een van de volgende problemen wijst:

* Een probleem met een netwerkverbinding. Controleer of de hostnaam voor SQL Database of SQL Managed instance toegankelijk is. Controleer ook of een firewall of netwerkbeveiligingsgroep (NSG) de toegang voor SSIS IR tot de server blokkeert.
* De aanmelding is mislukt tijdens SQL-verificatie. Met het opgegeven account kan niet bij de SQL Server-database worden aangemeld. Geef het juiste gebruikersaccount op.
* De aanmelding is mislukt tijdens de verificatie van Microsoft Azure Active Directory (Azure AD) (beheerde identiteit). Voeg de beheerde identiteit van uw factory toe aan een AAD-groep en zorg ervoor dat de beheerde identiteit toegangsmachtigingen heeft voor de server van de catalogusdatabase.
* Time-out van verbinding. Deze fout wordt altijd veroorzaakt door een beveiligingsconfiguratie. U wordt aangeraden dat u:
  1. Maak een nieuwe virtuele machine.
  1. Voeg de virtuele machine toe aan dezelfde Microsoft Azure Virtual Network van IR als IR zich in een virtueel netwerk bevindt.
  1. Installeer SSMS en controleer de status van het SQL Database of SQL Managed instance.

Voor andere problemen lost u het probleem op dat wordt weergegeven in het uitgebreide SQL-uitzonderingsfoutbericht. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteunings team van SQL Database of SQL Managed instance.

Als u de fout ziet wanneer IR wordt uitgevoerd, worden de netwerk beveiligings groep of de firewall wijzigingen waarschijnlijk verhinderd dat het SSIS IR worker-knoop punt toegang krijgt tot SQL Database of een door SQL beheerd exemplaar. Deblokkeren van het SSIS IR worker-knoop punt zodat het toegang heeft tot SQL Database of SQL Managed instance.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Dit type fout bericht kan er als volgt uitzien: ' de data base ' SSISDB ' heeft het quotum voor de grootte bereikt. Partitioneer of verwijder gegevens, verwijder indexen of Raadpleeg de documentatie voor mogelijke oplossingen. " 

De mogelijke oplossingen zijn:
* Verg root de quotum grootte van uw SSISDB.
* De configuratie van SSISDB wijzigen om de grootte te verkleinen met:
   * De retentie periode en het aantal project versies verminderen.
   * De retentie periode van het logboek te verminderen.
   * Het standaard niveau van het logboek wijzigen.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Deze fout betekent dat SQL Database of SQL Managed instance al een SSISDB heeft en dat het wordt gebruikt door een andere IR. U moet een andere SQL Database of een door SQL beheerd exemplaar opgeven, of de bestaande SSISDB verwijderen en de nieuwe IR opnieuw opstarten.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Deze fout kan een van de volgende oorzaken hebben:

* Het gebruikersaccount dat is geconfigureerd voor de SSIS IR beschikt niet over de machtiging om de database te maken. U kunt de gebruiker machtigen om de database te maken.
* Er treedt een time-out op tijdens het maken van de database, zoals een time-out tijdens uitvoering of een time-out voor de werking van de database. U kunt de bewerking later opnieuw uitvoeren. Als de nieuwe poging niet werkt, neemt u contact op met het ondersteunings team van SQL Database of SQL Managed instance.

Voor andere problemen controleert u het foutbericht van SQL-uitzondering en lost u het probleem op dat wordt vermeld in de foutdetails. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteunings team van SQL Database of SQL Managed instance.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Dit type fout bericht ziet er als volgt uit: "Ongeldige object naam catalog.catalog_properties". " In dit geval hebt u al een Data Base met de naam SSISDB, maar deze is niet gemaakt door SSIS IR, of de Data Base bevindt zich in een ongeldige status die wordt veroorzaakt door fouten in de laatste SSIS-IR-inrichting. U kunt de bestaande data base met de naam SSISDB verwijderen, maar u kunt ook een nieuw SQL Database of een door SQL beheerd exemplaar configureren voor de IR.

## <a name="custom-setup-issues"></a>Aangepaste installatie problemen

Aangepaste installatie biedt een interface voor het toevoegen van uw eigen installatiestappen tijdens het inrichten of opnieuw configureren van uw SSIS IR. Zie [Installatie aanpassen voor Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md) voor meer informatie.

Zorg ervoor dat uw container alleen de benodigde aangepaste installatiebestanden bevat. Alle bestanden in de container worden gedownload naar het SSIS IR-werkknooppunt. Het is raadzaam om het aangepaste installatiescript op een lokale machine te testen om eventuele problemen met het uitvoeren van scripts op te lossen voordat u het script uitvoert in SSIS IR.

De aangepaste container voor het installatiescript wordt gecontroleerd terwijl IR wordt uitgevoerd, omdat SSIS IR regelmatig wordt bijgewerkt. Voor deze update is toegang tot de container vereist om het aangepaste installatiescript te downloaden en opnieuw te installeren. Het proces controleert ook of de container toegankelijk is en of het bestand maim.cmd bestaat.

Voor elke fout die aangepaste installatie omvat, ziet u een CustomSetupScriptFailure-fout code met subcode zoals CustomSetupScriptBlobContainerInaccessible of CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Deze fout betekent dat SSIS IR geen toegang heeft tot uw Azure Blob-container voor aangepaste installatie. Zorg ervoor dat de SAS-URI van de container bereikbaar is en niet is verlopen.

Stop de IR als deze wordt uitgevoerd, configureer de IR opnieuw met de nieuwe aangepaste installatiecontainer SAS URI en start de IR opnieuw op.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Deze fout betekent dat SSIS IR geen aangepast installatiescript (main.cmd) kan vinden in de Blob-container. Zorg ervoor dat main.cmd in de container aanwezig is. Dit is het invoerpunt voor de aangepaste installatie.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Deze fout betekent dat de uitvoering van het aangepaste installatiescript (main.cmd) is mislukt. Voer het script eerst op de lokale machine uit of controleer de logboeken voor de uitvoering van de aangepaste installatie in uw Blob-container.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Deze fout wijst op een time-out voor het uitvoeren van een script voor een aangepaste installatie. Zorg ervoor dat het script op de achtergrond kan worden uitgevoerd en dat er geen interactieve invoer nodig is, en dat uw blobcontainer alleen de benodigde aangepaste installatiebestanden bevat. Het is raadzaam om het script eerst op de lokale computer te testen. Controleer ook de logboeken voor de uitvoering van de aangepaste installatie in uw Blob-container. De maximale periode voor aangepaste installatie is 45 minuten voordat er een time-out optreedt. De maximale periode omvat de tijd voor het downloaden van alle bestanden uit uw container en het installeren ervan op SSIS IR. Als u een langere periode nodig hebt, dient u een ondersteuningsticket in.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Deze fout betekent dat de poging om de logboeken voor de uitvoering van de aangepaste installatie in uw Blob-container te uploaden, is mislukt. Dit probleem treedt op omdat SSIS IR geen schrijfmachtigingen heeft voor uw Blob-container of vanwege opslag- of netwerkproblemen. Als de aangepaste installatie is geslaagd, is deze fout niet van invloed op een SSIS-functie, maar de logboeken ontbreken. Als de aangepaste installatie mislukt met een andere fout en het logboek niet wordt geüpload, wordt deze fout eerst gerapporteerd zodat het logboek voor analyse kan worden geüpload. Nadat dit probleem is opgelost, worden er ook meer specifieke problemen gerapporteerd. Als dit probleem na een nieuwe poging niet wordt opgelost, neemt u contact op met het Azure Data Factory-ondersteuningsteam.

## <a name="virtual-network-configuration"></a>Configuratie van virtueel netwerk

Als u SSIS IR toevoegt aan Azure Virtual Network, maakt SSIS IR gebruik van het virtuele netwerk onder dit gebruikersabonnement. Zie [Azure-SSIS Integration Runtime toevoegen aan een virtueel netwerk](./join-azure-ssis-integration-runtime-virtual-network.md) voor meer informatie.
Nadat SSIS IR is gestart, kunt u, als u problemen met de netwerk verbinding ondervindt, proberen om het probleem op te lossen met het [hulp programma voor diagnose verbindingen](ssis-integration-runtime-diagnose-connectivity-faq.md) .
Wanneer er een probleem optreedt dat is gerelateerd aan een virtueel netwerk, ziet u een van de volgende fouten.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Deze fout kan om verschillende redenen optreden. Raadpleeg de secties [Forbidden](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) en [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) om de problemen op te lossen.

### <a name="forbidden"></a>Verboden

Dit soort fout kan er als volgt uitzien: "SubnetId is niet ingeschakeld voor het huidige account. Microsoft.BatCH-resource provider is niet geregistreerd onder hetzelfde abonnement van VNet.

Deze details geven aan dat Azure Batch geen toegang heeft tot uw virtuele netwerk. Registreer de Microsoft.Batch-resourceprovider onder hetzelfde abonnement als Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Dit soort fout lijkt mogelijk op een van de volgende: 

- "Het opgegeven VNet bestaat niet of de batch-service heeft er geen toegang toe."
- "Het opgegeven subnet xxx bestaat niet."

Deze fouten geven aan dat het virtuele netwerk niet bestaat, de Azure Batch-service geen toegang tot het VNET heeft, of dat het opgegeven subnet niet bestaat.) Zorg ervoor dat het virtuele netwerk en het subnet bestaan, en dat Azure Batch er toegang toe heeft.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Dit type fout bericht kan er als volgt uitzien: ' kan geen Integration Runtime inrichten in VNet. Als de DNS-server-of NSG-instellingen zijn geconfigureerd, controleert u of de DNS-server toegankelijk is en of NSG juist is geconfigureerd. "

In deze situatie hebt u waarschijnlijk een aangepaste configuratie van de DNS-server of NSG-instellingen, waardoor de Azure-servernaam die is vereist voor SSIS IR, niet kan worden omgezet of weergegeven. Zie [Virtual Network-configuratie voor SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md) voor meer informatie. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR wordt regelmatig automatisch bijgewerkt. Er wordt een nieuwe Azure Batch-pool gemaakt tijdens de upgrade, en de oude Azure Batch-pool wordt verwijderd. Ook worden aan Virtual Network gerelateerde resources voor de oude pool verwijderd, en de nieuwe aan Virtual Network gerelateerde resources worden gemaakt onder uw abonnement. Deze fout geeft aan dat het verwijderen van aan Virtual Network gerelateerde resources voor de oude pool is mislukt vanwege een verwijderingsvergrendeling op het niveau van het abonnement of de resourcegroep. Aangezien de klant de verwijderingsvergrendeling beheert en instelt, moet in dit geval de klant de verwijderingsvergrendeling verwijderen.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Als het inrichten van SSIS IR mislukt, worden alle gemaakte resources verwijderd. Als er echter sprake is van een verwijderingsvergrendeling voor de resource op het niveau van het abonnement of de resourcegroep, worden Virtual Network-resources niet verwijderd zoals verwacht. Verwijder de verwijderingsvergrendeling en start de IR opnieuw om deze fout te herstellen.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

Als u SSIS IR stopt, worden alle aan Virtual Network gerelateerde resources verwijderd. Verwijderen kan echter mislukken als er sprake is van een verwijderingsvergrendeling voor de resource op het niveau van het abonnement of de resourcegroep. Ook hier wordt de verwijderingsvergrendeling beheerd en ingesteld door de klant. Daarom moet deze de verwijderingsvergrendeling verwijderen en SSIS IR opnieuw stoppen.

### <a name="nodeunavailable"></a>NodeUnavailable

Deze fout treedt op als IR actief is, en duidt erop dat IR is beschadigd. Deze fout wordt altijd veroorzaakt door een wijziging in de DNS-server of NSG-configuratie, waardoor SSIS IR geen verbinding kan maken met een noodzakelijke service. Omdat de configuratie van DNS-server en NSG wordt beheerd door de klant, moet deze de blokkerende problemen aan de eigen zijde oplossen. Zie [Virtual Network-configuratie voor SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md) voor meer informatie. Als u nog steeds problemen ondervindt, neemt u contact op met het ondersteuningsteam van Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configuratie van statische open bare IP-adressen

Wanneer u de Azure-SSIS IR aan Azure Virtual Network koppelt, kunt u ook uw eigen statische open bare IP-adressen voor de IR meebrengen, zodat de IR toegang kan krijgen tot gegevens bronnen waarmee de toegang tot specifieke IP-adressen wordt beperkt. Zie [Azure-SSIS Integration Runtime toevoegen aan een virtueel netwerk](./join-azure-ssis-integration-runtime-virtual-network.md) voor meer informatie.

Naast de bovenstaande problemen met het virtuele netwerk, kunt u ook voldoen aan statisch openbaar IP-adressen probleem. Raadpleeg de volgende fouten voor meer informatie.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

Deze fout kan om verschillende redenen optreden wanneer u de Azure-SSIS IR start:

| Foutbericht | Oplossing|
|:--- |:--- |
| Het geleverde statische open bare IP-adres wordt al gebruikt. Geef twee ongebruikte voor uw Azure-SSIS Integration Runtime op. | U moet twee ongebruikte statische open bare IP-adressen selecteren of de huidige verwijzingen naar het opgegeven open bare IP-adres verwijderen en vervolgens de Azure-SSIS IR opnieuw opstarten. |
| Het geleverde statische open bare IP-adres heeft geen DNS-naam. Geef twee van de DNS-naam op voor de Azure-SSIS Integration Runtime. | U kunt de DNS-naam van het open bare IP-adres in Azure Portal instellen, zoals hieronder wordt weer gegeven. Specifieke stappen zijn als volgt: (1) open Azure Portal en ga naar de pagina resource van dit open bare IP-adres. (2) Selecteer de **configuratie** sectie en stel de DNS-naam in en klik vervolgens op de knop **Opslaan** . (3) start uw Azure-SSIS IR opnieuw op. |
| De verschafte VNet-en statische open bare IP-adressen voor uw Azure-SSIS Integration Runtime moeten zich op dezelfde locatie bezien. | Volgens de vereisten van het Azure-netwerk moeten het statische open bare IP-adres en het virtuele netwerk zich op dezelfde locatie en hetzelfde abonnement benemen. Geef twee geldige statische open bare IP-adressen op en start de Azure-SSIS IR opnieuw. |
| Het geleverde statische open bare IP-adres is een basis. Geef twee standaard voor uw Azure-SSIS Integration Runtime op. | Raadpleeg [sku's van het open bare IP-adres](../virtual-network/public-ip-addresses.md#sku) voor hulp. |

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Als Azure-SSIS IR inrichting mislukt, worden alle resources die zijn gemaakt, verwijderd. Als er echter een resource delete Lock is voor het abonnement of de resource groep (die uw statische open bare IP-adres bevat), worden de netwerk bronnen niet zoals verwacht verwijderd. Als u de fout wilt herstellen, moet u de verwijderings vergrendeling verwijderen en de IR opnieuw opstarten.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Wanneer u Azure-SSIS IR stopt, worden alle netwerk resources verwijderd die zijn gemaakt in de resource groep met uw open bare IP-adres. Het verwijderen kan echter mislukken als de vergren deling van een resource wordt verwijderd uit het abonnement of de resource groep (die uw statische open bare IP-adres bevat). Verwijder de verwijderings vergrendeling en start de IR opnieuw.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure-SSIS IR wordt regel matig automatisch bijgewerkt. Nieuwe IR-knoop punten worden tijdens de upgrade gemaakt en de oude knoop punten worden verwijderd. Daarnaast worden de gemaakte netwerk bronnen (bijvoorbeeld de load balancer en de netwerk beveiligings groep) voor de oude knoop punten verwijderd en worden de nieuwe netwerk bronnen gemaakt onder uw abonnement. Deze fout betekent dat het verwijderen van de netwerk bronnen voor de oude knoop punten is mislukt vanwege een verwijderings vergrendeling bij het abonnement of de resource groep (die uw statische open bare IP-adres bevat). Verwijder de verwijderings vergrendeling zodat de oude knoop punten kunnen worden opgeruimd en het statische open bare IP-adres voor de oude knoop punten kan worden vrijgegeven. Als u dit niet doet, kan het statische open bare IP-adres niet worden vrijgegeven. we kunnen uw IR verder niet meer bijwerken.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Wanneer u uw eigen statische open bare IP-adressen wilt maken, moeten er twee open bare IP-adressen worden gegeven. Een van deze wordt gebruikt om de IR-knoop punten direct te maken en er wordt een andere gebruikt tijdens de upgrade van de IR. Deze fout kan optreden wanneer het andere open bare IP-adres niet kan worden gebruikt tijdens de upgrade. Raadpleeg  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) voor mogelijke oorzaken.