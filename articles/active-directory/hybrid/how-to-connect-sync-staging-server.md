---
title: 'Azure AD Connect synchronisatie: operationele taken en overwegingen | Microsoft Docs'
description: In dit onderwerp worden operationele taken voor Azure AD Connect Sync beschreven en wordt uitgelegd hoe u dit onderdeel kunt voorbereiden.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48584fa4042cf53fa1084e519dca0e64f530ca59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90090122"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: staging-server en herstel na nood gevallen
Met een server in de faserings modus kunt u wijzigingen aanbrengen in de configuratie en de wijzigingen bekijken voordat u de server actief maakt. U kunt ook volledige import en volledige synchronisatie uitvoeren om te controleren of alle wijzigingen worden verwacht voordat u deze wijzigingen aanbrengt in uw productie omgeving.

## <a name="staging-mode"></a>Faseringsmodus
De faserings modus kan worden gebruikt voor verschillende scenario's, waaronder:

* Hoge beschikbaarheid.
* Test en implementeer nieuwe configuratie wijzigingen.
* Een nieuwe server introduceren en de oude buiten gebruik stellen.

Tijdens de installatie kunt u de server in de **faserings modus** selecteren. Met deze actie wordt de server actief voor importeren en synchroniseren, maar worden er geen export bewerkingen uitgevoerd. Een server in de faserings modus voert geen wachtwoord synchronisatie of het terugschrijven van wacht woorden uit, zelfs niet als u deze functies tijdens de installatie hebt geselecteerd. Wanneer u de faserings modus uitschakelt, wordt het exporteren van de server gestart, wordt wachtwoord synchronisatie ingeschakeld en wordt het terugschrijven van wacht woorden ingeschakeld.

> [!NOTE]
> Stel dat u een Azure AD Connect hebt met de synchronisatie functie voor wachtwoord hash ingeschakeld. Wanneer u de faserings modus inschakelt, stopt de server met het synchroniseren van wachtwoord wijzigingen van on-premises AD. Wanneer u de faserings modus uitschakelt, wordt het synchroniseren van wachtwoord wijzigingen door de server hervat vanaf waar deze de laatste keer is gestopt. Als de server gedurende lange tijd in de faserings modus blijft, kan het even duren voordat de server alle wachtwoord wijzigingen synchroniseert die zijn opgetreden tijdens de periode.
>
>

U kunt een export nog steeds afdwingen met behulp van Synchronization Service Manager.

Een server in de faserings modus blijft wijzigingen van Active Directory en Azure AD ontvangen en kan de verantwoordelijkheden van een andere server snel overnemen in het geval van een storing. Als u configuratie wijzigingen aanbrengt aan uw primaire server, is het uw verantwoordelijkheid om dezelfde wijzigingen aan te brengen in de faserings modus van de server.

Voor die van u met kennis van oudere synchronisatie technologieën is de faserings modus anders omdat de server een eigen SQL database heeft. Met deze architectuur kan de faserings modus server zich in een ander Data Center bevinden.

### <a name="verify-the-configuration-of-a-server"></a>De configuratie van een server controleren
Voer de volgende stappen uit om deze methode toe te passen:

1. [Voorbereiden](#prepare)
2. [Configuratie](#configuration)
3. [Importeren en synchroniseren](#import-and-synchronize)
4. [Verifiëren](#verify)
5. [Scha kelen tussen actieve server](#switch-active-server)

#### <a name="prepare"></a>Voorbereiden
1. Installeer Azure AD Connect, selecteer de **faserings modus** en maak de selectie van **synchronisatie starten** op de laatste pagina in de installatie wizard. In deze modus kunt u de synchronisatie-engine hand matig uitvoeren.
   ![Scherm afbeelding toont de pagina gereed om te configureren in het dialoog venster Azure AD Connect.](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Meld u af/Meld u aan en selecteer **synchronisatie service** in het menu Start.

#### <a name="configuration"></a>Configuratie
Als u aangepaste wijzigingen hebt aangebracht aan de primaire server en de configuratie wilt vergelijken met de staging-server, gebruikt u [Azure AD Connect-configuratie Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importeren en synchroniseren
1. Selecteer **connectors** en selecteer de eerste connector met het type **Active Directory Domain Services**. Klik op **uitvoeren**, selecteer **volledig importeren** en **OK**. Voer de volgende stappen uit voor alle connectors van dit type.
2. Selecteer de connector met het type **Azure Active Directory (micro soft)**. Klik op **uitvoeren**, selecteer **volledig importeren** en **OK**.
3. Zorg ervoor dat de tabs-connectors nog steeds zijn geselecteerd. Klik voor elke connector met type **Active Directory Domain Services** op **uitvoeren**, selecteer **Delta synchronisatie** en **OK**.
4. Selecteer de connector met het type **Azure Active Directory (micro soft)**. Klik op **uitvoeren**, selecteer **Delta synchronisatie** en **OK**.

U hebt nu gefaseerd wijzigingen naar Azure AD en on-premises AD exporteren (als u Exchange Hybrid Deployment gebruikt). Met de volgende stappen kunt u controleren wat er moet worden gewijzigd voordat u de export naar de directory's start.

#### <a name="verify"></a>Verifiëren
1. Start een opdracht prompt en ga naar `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Uitvoeren: `csexport "Name of Connector" %temp%\export.xml /f:x` de naam van de connector bevindt zich in de synchronisatie service. Het heeft een naam die vergelijkbaar is met ' contoso.com-azure AD ' voor Azure AD.
3. Uitvoeren: er `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` bevindt zich een bestand in% Temp% met de naam export.csv dat in micro soft Excel kan worden onderzocht. Dit bestand bevat alle wijzigingen die moeten worden geëxporteerd.
4. Breng de benodigde wijzigingen aan in de gegevens of configuratie en voer deze stappen opnieuw uit (Importeer en synchroniseer en controleer) totdat de wijzigingen die worden geëxporteerd, worden verwacht.

**Wat is het export.csv bestand** ? Het meren deel van het bestand is zelf uitleg. Enkele afkortingen voor het begrijpen van de inhoud:
* OMODT: object wijzigings type. Hiermee wordt aangegeven of de bewerking op object niveau een toevoegen, bijwerken of verwijderen is.
* AMODT – kenmerk wijzigings type. Hiermee wordt aangegeven of de bewerking op kenmerk niveau een add, update of Delete is.

**Algemene Id's ophalen** Het export.csv bestand bevat alle wijzigingen die moeten worden geëxporteerd. Elke rij komt overeen met een wijziging voor een object in de connector ruimte en het object wordt geïdentificeerd door het kenmerk DN. Het kenmerk DN is een unieke id die is toegewezen aan een object in de connector ruimte. Wanneer u veel rijen/wijzigingen hebt in de export.csv om te analyseren, kan het lastig zijn om erachter te komen welke objecten de wijzigingen hebben op basis van het kenmerk DN. Gebruik het Power shell-script csanalyzer.ps1 om het proces van het analyseren van de wijzigingen te vereenvoudigen. Het script haalt algemene id's op (bijvoorbeeld displayName, userPrincipalName) van de objecten. Het script gebruiken:
1. Kopieer het Power shell-script uit de sectie [CSAnalyzer](#appendix-csanalyzer) naar een bestand met de naam `csanalyzer.ps1` .
2. Open een Power shell-venster en blader naar de map waarin u het Power shell-script hebt gemaakt.
3. Uitvoeren: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. U hebt nu een bestand met de naam **processedusers1.csv** dat in micro soft Excel kan worden onderzocht. Houd er rekening mee dat het bestand een toewijzing bevat van het kenmerk DN aan algemene id's (bijvoorbeeld displayName en userPrincipalName). Het bevat momenteel niet de daad werkelijke kenmerk wijzigingen die moeten worden geëxporteerd.

#### <a name="switch-active-server"></a>Scha kelen tussen actieve server
1. Schakel op de huidige actieve server de server (DirSync/FIM/Azure AD Sync) uit zodat deze niet wordt geëxporteerd naar Azure AD of stel deze in de faserings modus (Azure AD Connect) in.
2. Voer de installatie wizard uit op de server in de **faserings modus** en schakel de **faserings modus** uit.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Herstel na noodgeval
Een deel van het implementatie ontwerp is het plannen van wat er moet gebeuren als er sprake is van een nood geval waarin u de synchronisatie server kwijtraakt. Er zijn verschillende modellen die u kunt gebruiken, afhankelijk van verschillende factoren, waaronder:

* Wat is uw tolerantie voor het aanbrengen van wijzigingen in objecten in azure AD tijdens de downtime?
* Als u wachtwoord synchronisatie gebruikt, accepteren de gebruikers dat ze het oude wacht woord in azure AD moeten gebruiken als ze het op locatie wijzigen?
* Hebt u een afhankelijkheid van real-time bewerkingen, zoals wacht woord terugschrijven?

Afhankelijk van de antwoorden op deze vragen en het beleid van uw organisatie, kan een van de volgende strategieën worden geïmplementeerd:

* Opnieuw samen stellen wanneer dit nodig is.
* Beschikken over een reserve server voor stand-by, ook wel de **faserings modus** genoemd.
* Virtuele machines gebruiken.

Als u de ingebouwde SQL Express-data base niet gebruikt, raadpleegt u ook de sectie [SQL maximale Beschik baarheid](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Opnieuw samen stellen wanneer dit nodig is
Een geschikte strategie is het plannen van het opnieuw bouwen van een server als dat nodig is. Normaal gesp roken wordt de synchronisatie-engine geïnstalleerd en worden de initiële import-en synchronisatie taken binnen een paar uur voltooid. Als er geen reserve server beschikbaar is, is het mogelijk om tijdelijk een domein controller te gebruiken voor het hosten van de synchronisatie-engine.

De synchronisatie-engine-server slaat geen status over de objecten op, zodat de data base opnieuw kan worden samengesteld op basis van de gegevens in Active Directory en Azure AD. Het kenmerk **Source Anchor** wordt gebruikt om de objecten van on-premises en de Cloud samen te voegen. Als u de server opnieuw opbouwt met bestaande objecten on-premises en de Cloud, komt de synchronisatie-engine opnieuw overeen met die objecten bij het opnieuw installeren. De dingen die u nodig hebt om documenten te documenteren en op te slaan, zijn de configuratie wijzigingen die zijn aangebracht op de server, zoals filter-en synchronisatie regels. Deze aangepaste configuraties moeten opnieuw worden toegepast voordat u begint met synchronisatie.

### <a name="have-a-spare-standby-server---staging-mode"></a>Een reserve server voor stand-by-staging hebben
Als u een complexere omgeving hebt, is het raadzaam om een of meer stand-by-servers te hebben. Tijdens de installatie kunt u instellen dat een server zich in de **faserings modus** bevindt.

Zie voor meer informatie de [faserings modus](#staging-mode).

### <a name="use-virtual-machines"></a>Virtuele machines gebruiken
Een veelvoorkomende en ondersteunde methode is het uitvoeren van de synchronisatie-engine in een virtuele machine. Als er een probleem is met de host, kan de installatie kopie met de synchronisatie engine-server worden gemigreerd naar een andere server.

### <a name="sql-high-availability"></a>SQL-hoge Beschik baarheid
Als u niet de SQL Server Express gebruikt die wordt geleverd met Azure AD Connect, moet er ook een hoge Beschik baarheid voor SQL Server worden overwogen. De ondersteunde oplossingen voor hoge Beschik baarheid zijn onder andere SQL Clustering en AOA (AlwaysOn-beschikbaarheids groepen). Niet-ondersteunde oplossingen bevatten mirroring.

Er is ondersteuning voor SQL AOA toegevoegd aan Azure AD Connect in versie 1.1.524.0. U moet SQL AOA inschakelen voordat u Azure AD Connect installeert. Tijdens de installatie detecteert Azure AD Connect of het gegeven SQL-exemplaar is ingeschakeld voor SQL AOA of niet. Als SQL-AOA is ingeschakeld, Azure AD Connect meer cijfers uit als SQL AOA is geconfigureerd voor het gebruik van synchrone replicatie of asynchrone replicatie. Bij het instellen van de listener voor de beschikbaarheids groep wordt aanbevolen de eigenschap RegisterAllProvidersIP in te stellen op 0. Dit komt omdat Azure AD Connect momenteel gebruikt SQL Native Client om verbinding te maken met SQL en SQL Native Client het gebruik van de eigenschap MultiSubNetFailover niet ondersteunt.

## <a name="appendix-csanalyzer"></a>Bijlage CSAnalyzer
Zie de sectie [controleren](#verify) hoe u dit script gebruikt.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Volgende stappen
**Overzichts onderwerpen**  

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)  
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)  
