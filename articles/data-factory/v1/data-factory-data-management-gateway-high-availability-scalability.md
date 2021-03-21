---
title: Hoge Beschik baarheid met Data Management Gateway in Azure Data Factory
description: In dit artikel wordt uitgelegd hoe u een Data Management Gateway kunt schalen door meer knoop punten toe te voegen en omhoog te schalen door het aantal gelijktijdige taken te verhogen dat op een knoop punt kan worden uitgevoerd.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: ad34ed14682d729157f45e67eb3e0d3bb3eb39b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100391725"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>Data Management Gateway-hoge Beschik baarheid en schaal baarheid (preview-versie)
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [zelf-hostende Integration runtime in](../create-self-hosted-integration-runtime.md). 


Dit artikel helpt u bij het configureren van een oplossing met hoge Beschik baarheid en schaal baarheid met Data Management Gateway/integratie.    

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat u al bekend bent met de basis principes van Integration Runtime (eerder Data Management Gateway). Als dat niet het geval is, raadpleegt u [Data Management Gateway](data-factory-data-management-gateway.md).
> 
> **Deze preview-functie wordt officieel ondersteund in Data Management Gateway versie 2.12. xxxx. x en hoger**. Zorg ervoor dat u versie 2.12. xxxx. x of hoger gebruikt. Down load [hier](https://www.microsoft.com/download/details.aspx?id=39717)de nieuwste versie van Data Management Gateway.

## <a name="overview"></a>Overzicht
U kunt Data Management gateways die zijn geïnstalleerd op meerdere on-premises machines koppelen aan één logische gateway vanuit de portal. Deze computers worden **knoop punten** genoemd. U kunt Maxi maal **vier knoop punten** koppelen aan een logische gateway. De voor delen van het gebruik van meerdere knoop punten (on-premises machines met Gateway geïnstalleerd) voor een logische gateway zijn:  

- Verbeter de prestaties van het verplaatsen van gegevens tussen on-premises en gegevens opslag in de Cloud.  
- Als een van de knoop punten om een of andere reden niet meer wordt weer gegeven, zijn er nog andere knoop punten beschikbaar voor het verplaatsen van de gegevens. 
- Als een van de knoop punten offline moet worden genomen voor onderhoud, zijn er nog andere knoop punten beschikbaar voor het verplaatsen van de gegevens.

U kunt ook het aantal **gelijktijdige taken voor gegevens verplaatsing** configureren dat kan worden uitgevoerd op een knoop punt om de mogelijkheid te schalen om gegevens te verplaatsen tussen on-premises en gegevens archieven in de Cloud. 

Met behulp van de Azure Portal kunt u de status van deze knoop punten bewaken, zodat u kunt beslissen of u een knoop punt wilt toevoegen aan of verwijderen uit de logische gateway. 

## <a name="architecture"></a>Architectuur 
In het volgende diagram vindt u een overzicht van de architectuur van de functie voor schaal baarheid en beschik baarheid van de Data Management Gateway: 

![Data Management Gateway-hoge Beschik baarheid en schaal baarheid](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png)

Een **logische gateway** is de gateway die u toevoegt aan een Data Factory in het Azure Portal. Eerder kunt u slechts één on-premises Windows-machine koppelen met Data Management Gateway geïnstalleerd met een logische gateway. Deze on-premises gateway computer wordt een knoop punt genoemd. U kunt nu Maxi maal **vier fysieke knoop punten** koppelen aan een logische gateway. Een logische gateway met meerdere knoop punten wordt een **multi-knoop punt gateway** genoemd.  

Al deze knoop punten zijn **actief**. Ze kunnen gegevens verplaatsings taken verwerken om gegevens te verplaatsen tussen on-premises en gegevens opslag in de Cloud. Een van de knoop punten fungeert als zowel de verzender als de werk nemer. Andere knoop punten in de groepen zijn worker-knoop punten. Een **dispatcher** -knoop punt haalt taken/taken voor gegevens verplaatsing op uit de Cloud service en verzendt deze naar werk knooppunten (waaronder zichzelf). Een **worker** -knoop punt voert gegevens verplaatsings taken uit om gegevens te verplaatsen tussen on-premises en gegevens opslag in de Cloud. Alle knoop punten zijn werk nemers. Er kan slechts één knoop punt worden verzonden en worker.    

U kunt normaal gesp roken beginnen met één knoop punt en uitschalen om meer knoop punten toe te voegen, omdat het bestaande knoop punt (en) **verouderd** blijven met de belasting voor het verplaatsen van gegevens. U kunt ook de capaciteit van gegevens verplaatsing van een gateway knooppunt **opschalen** door het aantal gelijktijdige taken dat op het knoop punt mag worden uitgevoerd, te verhogen. Deze mogelijkheid is ook beschikbaar met een gateway met één knoop punt (zelfs wanneer de functie voor schaal baarheid en beschik baarheid niet is ingeschakeld). 

Een gateway met meerdere knoop punten houdt de referenties van het gegevens archief synchroon op alle knoop punten. Als er sprake is van een verbindings probleem tussen knoop punten, zijn de referenties mogelijk niet synchroon. Wanneer u referenties instelt voor een on-premises gegevens opslag die gebruikmaakt van een gateway, slaat de referenties op het knoop punt dispatcher/worker op. Het dispatcher-knoop punt wordt gesynchroniseerd met andere worker-knoop punten. Dit proces wordt synchronisatie van **referenties** genoemd. Het communicatie kanaal tussen knoop punten kan worden **versleuteld** met een openbaar SSL/TLS-certificaat. 

## <a name="set-up-a-multi-node-gateway"></a>Een gateway met meerdere knoop punten instellen
In deze sectie wordt ervan uitgegaan dat u de volgende twee artikelen hebt door lopen of dat u bekend bent met de concepten in deze artikelen: 

- [Data Management Gateway](data-factory-data-management-gateway.md) -biedt een gedetailleerd overzicht van de gateway.
- [Gegevens verplaatsen tussen on-premises en Cloud gegevens archieven](data-factory-move-data-between-onprem-and-cloud.md) : bevat een overzicht met stapsgewijze instructies voor het gebruik van een gateway met een enkel knoop punt.  

> [!NOTE]
> Zie vereisten die worden vermeld in [het hoofd artikel](data-factory-data-management-gateway.md#prerequisites)voordat u een Data Management-Gateway installeert op een on-premises Windows-computer.

1. In de [Stapsgewijze instructies](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)bij het maken van een logische gateway, schakelt u de functie voor **maximale beschik baarheid & schaal baarheid** in. 

    ![Data Management Gateway-hoge Beschik baarheid en schaal baarheid inschakelen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png)
2. Op de pagina **configureren** gebruikt u de koppeling **snelle installatie** of **hand matige installatie** om een gateway te installeren op het eerste knoop punt (een on-premises Windows-machine).

    ![Data Management Gateway-Express of hand matige installatie](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png)

    > [!NOTE]
    > Als u de optie snelle installatie gebruikt, wordt de communicatie tussen knoop punten uitgevoerd zonder versleuteling. De naam van het knoop punt is hetzelfde als de naam van de computer. Gebruik hand matige installatie als de communicatie tussen knoop punten moet worden versleuteld of als u een knooppunt naam wilt opgeven van uw keuze. Knooppunt namen kunnen niet later worden bewerkt.
3. Als u **snelle installatie** kiest
    1. U ziet het volgende bericht nadat de gateway is geïnstalleerd:

        ![Data Management Gateway-snelle installatie geslaagd](media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png)
    2. Start Gegevensbeheer Configuration Manager voor de gateway door [deze instructies](data-factory-data-management-gateway.md#configuration-manager)te volgen. U ziet de naam van de gateway, de naam van het knoop punt, de status, enzovoort.

        ![Scherm afbeelding die laat zien waar u de naam van de gateway, de knooppunt naam en de status kunt zien.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)
4. Als u **hand matig instellen** kiest:
    1. Down load het installatie pakket van het micro soft Download centrum en voer het uit om de gateway op uw computer te installeren.
    2. Gebruik de **verificatie sleutel** van de pagina **configureren** om de gateway te registreren.
    
        ![Scherm afbeelding die laat zien waar de verificatie sleutel moet worden gebruikt.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png)
    3. Op de pagina **Nieuw gateway knooppunt** kunt u een aangepaste **naam** opgeven voor het gateway-knoop punt. De naam van het knoop punt is standaard hetzelfde als de naam van de computer.    

        ![Data Management Gateway: Geef een naam op](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png)
    4. Op de volgende pagina kunt u kiezen of u **versleuteling wilt inschakelen voor communicatie tussen knoop** punten. Klik op **overs Laan** om versleuteling uit te scha kelen (standaard).

        ![Data Management Gateway-versleuteling inschakelen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png)  
    
        > [!NOTE]
        > Het wijzigen van de versleutelings modus wordt alleen ondersteund als u één gateway-knoop punt in de logische gateway hebt. Als u de versleutelings modus wilt wijzigen wanneer een gateway meerdere knoop punten heeft, voert u de volgende stappen uit: Verwijder alle knoop punten, met uitzonde ring van één knoop punt, wijzig de versleutelings modus en voeg de knoop punten opnieuw toe.
        > 
        > Zie de sectie met [TLS/SSL-certificaat vereisten](#tlsssl-certificate-requirements) voor een lijst met vereisten voor het gebruik van een TLS/SSL-certificaat. 
    5. Nadat de gateway is geïnstalleerd, klikt u op Start Configuration Manager:
    
        ![Hand matige installatie-Configuration Manager starten](media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png)     
    6. u ziet Data Management Gateway Configuration Manager op het knoop punt (on-premises Windows-machine), waarin de status van de verbinding, de **Gateway naam** en de naam van het **knoop punt** worden weer gegeven.  

        ![Data Management Gateway-installatie voltooid](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png)

        > [!NOTE]
        > Als u de gateway op een virtuele Azure-machine inricht, kunt u [deze Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-mutiple-vms-with-data-management-gateway)gebruiken. Met dit script maakt u een logische gateway, stelt u Vm's in waarop Data Management Gateway software is geïnstalleerd en registreert u deze bij de logische gateway. 
6. Start de pagina **Gateway** in azure portal: 
    1. Klik op de start pagina van data factory in de portal op **gekoppelde services**.
    
        ![Scherm afbeelding die de tegel gekoppelde services markeert.](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png)
    2. Selecteer de **Gateway** om de pagina **Gateway** weer te geven:
    
        ![Startpagina van de gegevensfactory](media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png)
    4. U ziet de pagina **Gateway** :   

        ![Gateway met een weer gave met één knoop punt](media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png) 
7. Klik op **knoop punt toevoegen** op de werk balk om een knoop punt toe te voegen aan de logische gateway. Als u van plan bent snelle installatie te gebruiken, voert u deze stap uit vanaf de on-premises computer die wordt toegevoegd als een knoop punt aan de gateway. 

    ![Data Management Gateway-menu knoop punt toevoegen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)
8. De stappen zijn vergelijkbaar met het instellen van het eerste knoop punt. Met de Configuration Manager gebruikers interface kunt u de naam van het knoop punt instellen als u de optie hand matige installatie kiest: 

    ![Configuration Manager-tweede gateway installeren](media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png)
9. Nadat de gateway is geïnstalleerd op het knoop punt, wordt het volgende scherm weer gegeven in het hulp programma Configuration Manager:  

    ![Configuration Manager-tweede gateway installeren is voltooid](media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png)
10. Als u de **Gateway** pagina in de portal opent, ziet u nu twee gateway knooppunten: 

    ![Gateway met twee knoop punten in de portal](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)
11. Als u een gateway knooppunt wilt verwijderen, klikt u op **knoop punt verwijderen** op de werk balk, selecteert u het knoop punt dat u wilt verwijderen en klikt u vervolgens op **verwijderen** op de werk balk. Met deze actie wordt het geselecteerde knoop punt uit de groep verwijderd. Houd er rekening mee dat met deze actie de Data Management Gateway-software niet wordt verwijderd van het knoop punt (on-premises Windows-machine). Gebruik **Program ma's installeren of verwijderen** in het configuratie scherm op de on-premises om de gateway te verwijderen. Wanneer u de gateway uit het knoop punt verwijdert, wordt deze automatisch verwijderd uit de portal.   

## <a name="upgrade-an-existing-gateway"></a>Een bestaande gateway bijwerken
U kunt een bestaande gateway bijwerken voor gebruik van de functie hoge Beschik baarheid en schaal baarheid. Deze functie werkt alleen met knoop punten die de Data Management Gateway van versie >= 2.12. xxxx hebben. U kunt de versie van de Data Management gateway die op een computer is geïnstalleerd, bekijken op het tabblad **Help** van de Data Management Gateway Configuration Manager. 

1. Werk de gateway op de on-premises machine bij naar de meest recente versie door de volgende MSI-installatie pakket te downloaden en uit te voeren vanuit het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=39717). Zie de sectie [installatie](data-factory-data-management-gateway.md#installation) voor meer informatie.  
2. Navigeer naar de Azure Portal. Start de **Data Factory-pagina** voor uw Data Factory. Klik op de tegel gekoppelde services om de **pagina gekoppelde services** te starten. Selecteer de gateway om de **Gateway pagina** te starten. Klik en Schakel **Preview-functie** in, zoals wordt weer gegeven in de volgende afbeelding: 

    ![Data Management Gateway-preview-functie inschakelen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png)   
2. Zodra de preview-functie is ingeschakeld in de portal, sluit u alle pagina's. Open de **pagina gateway** opnieuw om de nieuwe preview-gebruikers interface (UI) weer te geven.
 
    ![Data Management Gateway-een preview-functie inschakelen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png)

    ![Gebruikers interface van Data Management Gateway-preview](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png)

    > [!NOTE]
    > Tijdens de upgrade is de naam van het eerste knoop punt de naam van de computer. 
3. Voeg nu een knoop punt toe. Klik op de pagina **Gateway** op **knoop punt toevoegen**.  

    ![Data Management Gateway-menu knoop punt toevoegen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png)

    Volg de instructies in de vorige sectie om het knoop punt in te stellen. 

### <a name="installation-best-practices"></a>Best practices voor installatie

- Configureer het energiebeheer schema op de hostcomputer voor de gateway, zodat de computer niet in de sluimer stand wordt gezet. Als de hostmachine in de slaap stand wordt gezet, reageert de gateway niet op gegevens aanvragen.
- Maak een back-up van het certificaat dat is gekoppeld aan de gateway.
- Zorg ervoor dat alle knoop punten een vergelijk bare configuratie hebben (aanbevolen) voor optimale prestaties. 
- Voeg ten minste twee knoop punten toe om hoge Beschik baarheid te garanderen.  

### <a name="tlsssl-certificate-requirements"></a>Vereisten voor TLS/SSL-certificaat
Hier volgen de vereisten voor het TLS/SSL-certificaat dat wordt gebruikt voor het beveiligen van de communicatie tussen de knoop punten van Integration runtime:

- Het certificaat moet een openbaar vertrouwd x509 v3-certificaat zijn. U wordt aangeraden certificaten te gebruiken die zijn uitgegeven door een open bare certificerings instantie (CA) van derden.
- Elk knoop punt voor Integration runtime moet dit certificaat vertrouwen, evenals de client computer waarop de toepassing Credential Manager wordt uitgevoerd. 
  > [!NOTE]
  > Credential Manager-toepassing wordt gebruikt bij het veilig instellen van de referentie van de wizard kopiëren/Azure Portal. En dit kan worden gestart vanaf elke computer binnen hetzelfde netwerk als de on-premises/persoonlijke gegevens opslag.
- Joker tekens worden ondersteund. Als de FQDN-naam **node1.domain.contoso.com** is, kunt u **_. domain.contoso.com_* als onderwerpnaam van het certificaat gebruiken.
- SAN-certificaten worden niet aanbevolen omdat alleen het laatste item van de alternatieve namen voor het onderwerp wordt gebruikt en alle andere worden genegeerd vanwege de huidige beperking. Bijvoorbeeld u hebt een SAN-certificaat waarvan het SAN **node1.domain.contoso.com** en **node2.domain.contoso.com** is. u kunt dit certificaat alleen gebruiken op de computer waarvan de FQDN **node2.domain.contoso.com** is.
- Ondersteunt elke sleutel grootte die wordt ondersteund door Windows Server 2012 R2 voor TLS/SSL-certificaten.
- Certificaten met CNG-sleutels worden niet ondersteund.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>Veelgestelde vragen: wanneer kan ik deze versleuteling niet inschakelen?
Als u versleuteling inschakelt, kunt u bepaalde kosten toevoegen aan uw infra structuur (openbaar certificaat), dus u kunt het inschakelen van versleuteling in de onderstaande gevallen overs Laan:
- Wanneer de Integration runtime wordt uitgevoerd op een vertrouwd netwerk of een netwerk met transparante versleuteling, zoals IP/SEC. Omdat deze kanaal communicatie alleen binnen uw vertrouwde netwerk beperkt is, hebt u mogelijk geen extra versleuteling nodig.
- Wanneer de Integration runtime niet wordt uitgevoerd in een productie omgeving. Dit kan helpen bij het verminderen van de kosten van TLS/SSL-certificaten.


## <a name="monitor-a-multi-node-gateway"></a>Een gateway met meerdere knoop punten bewaken
### <a name="multi-node-gateway-monitoring"></a>Gateway bewaking met meerdere knoop punten
In de Azure Portal kunt u bijna realtime een moment opname van het resource gebruik (CPU, geheugen, netwerk (in/uit), enzovoort) op elk knoop punt samen met de status van Gateway knooppunten weer geven. 

![Data Management Gateway-bewaking van meerdere knoop punten](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png)

U kunt **Geavanceerde instellingen** inschakelen op de **pagina gateway** om geavanceerde metrische gegevens weer te geven, zoals **netwerk**(in/uitgaand), **functie & referentie status**, die nuttig is in fout opsporing voor gateway problemen en **gelijktijdige taken** (uitvoeren/beperken) die dienovereenkomstig kunnen worden gewijzigd of gewijzigd tijdens het afstemmen van de prestaties. De volgende tabel bevat beschrijvingen van de kolommen in de lijst met **Gateway knooppunten** :  

Bewakings eigenschap | Beschrijving
:------------------ | :---------- 
Name | De naam van de logische gateway en knoop punten die zijn gekoppeld aan de gateway.  
Status | Status van de logische gateway en de gateway knooppunten. Voor beeld: online/offline/beperkt/etc. Zie de sectie [Gateway status](#gateway-status) voor meer informatie over deze statussen. 
Versie | Hier wordt de versie van de logische gateway en elk gateway knooppunt weer gegeven. De versie van de logische gateway wordt bepaald op basis van de versie van de knoop punten in de groep. Als er knoop punten met verschillende versies in de installatie van de logische gateway zijn, hebben alleen de knoop punten met hetzelfde versie nummer als de logische gateway goed functioneren. Andere bevinden zich in de beperkte modus en moeten hand matig worden bijgewerkt (alleen als de automatische update mislukt). 
Beschikbaar geheugen | Beschikbaar geheugen op een gateway-knoop punt. Deze waarde is een bijna realtime moment opname. 
CPU-gebruik | CPU-gebruik van een gateway knooppunt. Deze waarde is een bijna realtime moment opname. 
Netwerken (in/uit) | Netwerk gebruik van een gateway-knoop punt. Deze waarde is een bijna realtime moment opname. 
Gelijktijdige taken (uitvoeren/beperken) | Aantal taken of taken dat op elk knoop punt wordt uitgevoerd. Deze waarde is een bijna realtime moment opname. De limiet is het maximale aantal gelijktijdige taken voor elk knoop punt. Deze waarde wordt gedefinieerd op basis van de grootte van de machine. U kunt de limiet verhogen voor het opschalen van gelijktijdige taak uitvoering in geavanceerde scenario's, waarbij CPU/geheugen/netwerk wordt gebruikt, maar activiteiten een time-out hebben. Deze mogelijkheid is ook beschikbaar met een gateway met één knoop punt (zelfs wanneer de functie voor schaal baarheid en beschik baarheid niet is ingeschakeld). Zie sectie [Scale-overwegingen](#scale-considerations) voor meer informatie. 
Rol | Er zijn twee soorten rollen: dispatcher en worker. Alle knoop punten zijn werk nemers, wat betekent dat ze allemaal kunnen worden gebruikt om taken uit te voeren. Er is slechts één dispatcher-knoop punt, dat wordt gebruikt om taken/taken uit te geven vanuit Cloud Services en deze te verzenden naar verschillende werk knooppunten (waaronder zichzelf). 

![Data Management Gateway-geavanceerde bewaking van meerdere knoop punten](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png)

### <a name="gateway-status"></a>Gateway status

De volgende tabel bevat mogelijke statussen van een **Gateway knooppunt**: 

Status    | Opmerkingen/Scenario's
:------- | :------------------
Online | Het knoop punt dat is verbonden met Data Factory service.
Offline | Het knoop punt is offline.
Upgrade uitvoeren | Het knoop punt wordt automatisch bijgewerkt.
Beperkt | Vanwege een connectiviteits probleem. Wordt mogelijk veroorzaakt door een probleem met de HTTP-poort 8050, het connectiviteits probleem van de service bus of het probleem met de synchronisatie van referenties. 
Niet-actief | Het knoop punt bevindt zich in een configuratie die verschilt van de configuratie van andere hoofd knooppunten.<br/><br/> Een knoop punt kan inactief zijn wanneer er geen verbinding kan worden gemaakt met andere knoop punten. 


De volgende tabel bevat mogelijke statussen van een **logische gateway**. De status van de gateway is afhankelijk van de status van de gateway-knoop punten. 

Status | Opmerkingen
:----- | :-------
Registratie vereist | Er is nog geen knoop punt geregistreerd voor deze logische gateway
Online | Gateway knooppunten zijn online
Offline | Geen knoop punt in online status.
Beperkt | Niet alle knoop punten in deze gateway hebben de status in orde. Deze status is een waarschuwing dat een deel van het knoop punt mogelijk niet beschikbaar is. <br/><br/>Kan worden veroorzaakt door een probleem met de referenties voor de verzender/het worker-knoop punt. 

### <a name="pipeline-activities-monitoring"></a>Bewaking van pijp lijn/activiteiten
De Azure Portal biedt een pipeline-bewakings ervaring met gedetailleerde Details over knooppunt niveau. Het laat bijvoorbeeld zien welke activiteiten er zijn uitgevoerd op welk knoop punt. Deze informatie kan nuttig zijn bij het vaststellen van prestatie problemen op een bepaald knoop punt, vanwege netwerk beperking. 

![Data Management Gateway-bewaking van meerdere knoop punten voor pijp lijnen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png)

![Data Management Gateway-pijplijn Details](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png)

## <a name="scale-considerations"></a>Schaal overwegingen

### <a name="scale-out"></a>Uitschalen
Wanneer het **beschik bare geheugen laag is** en het **CPU-gebruik hoog is**, kan het toevoegen van een nieuw knoop punt het laden van de belasting over machines opschalen. Als er een fout optreedt in het mislukken van de activiteiten omdat een time-out of gateway knooppunt offline is, is het handig als u een knoop punt aan de gateway toevoegt.
 
### <a name="scale-up"></a>Omhoog schalen
Wanneer het beschik bare geheugen en de CPU niet goed worden gebruikt, maar de niet-actieve capaciteit 0 is, moet u omhoog schalen door het aantal gelijktijdige taken dat op een knoop punt kan worden uitgevoerd, te verhogen. Het kan ook zijn dat u wilt opschalen wanneer activiteiten uitvallen omdat de gateway overbelast is. Zoals in de volgende afbeelding wordt weer gegeven, kunt u de maximale capaciteit voor een knoop punt verhogen. We raden u aan om te beginnen met.  

![Data Management Gateways schalen](media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png)


## <a name="known-issuesbreaking-changes"></a>Bekende problemen/verbrekings wijzigingen

- Op dit moment kunt u Maxi maal vier fysieke gateway knooppunten hebben voor één logische gateway. Als u meer dan vier knoop punten nodig hebt om prestatie redenen, stuurt u een e-mail naar [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com) .
- U kunt een gateway knooppunt met de verificatie sleutel van een andere logische gateway niet opnieuw registreren om over te scha kelen van de huidige logische gateway. Als u zich opnieuw wilt registreren, verwijdert u de gateway uit het knoop punt, installeert u de gateway opnieuw en registreert u deze met de verificatie sleutel voor de andere logische gateway. 
- Als HTTP-proxy is vereist voor alle gateway knooppunten, stelt u de proxy in diahost.exe.config en diawp.exe.config en gebruikt u Server beheer om ervoor te zorgen dat alle knoop punten dezelfde diahost.exe.config en diawip.exe.config hebben. Zie de sectie [proxy-instellingen configureren](data-factory-data-management-gateway.md#configure-proxy-server-settings) voor meer informatie. 
- Als u de versleutelings modus voor communicatie tussen knoop punten in de gateway Configuration Manager wilt wijzigen, verwijdert u alle knoop punten in de portal, met uitzonde ring van één. Voeg vervolgens de knoop punten terug nadat u de versleutelings modus hebt gewijzigd.
- Gebruik een officieel TLS-certificaat als u ervoor kiest het communicatie kanaal tussen knoop punten te versleutelen. Zelfondertekend certificaat kan verbindings problemen veroorzaken omdat hetzelfde certificaat mogelijk niet wordt vertrouwd in de certificerings instantie lijst op andere computers. 
- U kunt een gateway knooppunt niet registreren bij een logische gateway wanneer de versie van het knoop punt lager is dan de versie van de logische gateway. Verwijder alle knoop punten van de logische gateway uit de portal, zodat u een lagere versie van het knoop punt (downgrade) kunt registreren. Als u alle knoop punten van een logische Gateway verwijdert, installeert en registreert u de nieuwe knoop punten hand matig op die logische gateway. Snelle installatie wordt in dit geval niet ondersteund.
- U kunt geen snelle installatie gebruiken om knoop punten te installeren op een bestaande logische gateway, die nog steeds Cloud referenties gebruikt. U kunt controleren waar de referenties worden opgeslagen vanaf de gateway Configuration Manager op het tabblad instellingen.
- U kunt geen snelle installatie gebruiken om knoop punten te installeren op een bestaande logische gateway, waarvoor versleuteling van knoop punt naar knoop punt is ingeschakeld. Als het instellen van de versleutelings modus hand matig certificaten moet toevoegen, is snelle installatie niet meer een optie. 
- Voor het kopiëren van bestanden vanuit een on-premises omgeving moet u geen \\ localhost of C:\files meer gebruiken, omdat localhost of lokaal station mogelijk niet toegankelijk is via alle knoop punten. Gebruik in plaats daarvan \\ ServerName\files om de locatie van de bestanden op te geven.


## <a name="rolling-back-from-the-preview"></a>Terugdraaien vanuit de preview 
Als u de preview-versie wilt herstellen, verwijdert u alle knoop punten, maar één knoop punt. Het maakt niet uit welke knoop punten u verwijdert, maar zorg ervoor dat u ten minste één knoop punt in de logische gateway hebt. U kunt een knoop punt verwijderen door de gateway op de computer of door de Azure Portal te gebruiken. Klik in de Azure Portal op de pagina **Data Factory** op gekoppelde services om de pagina **gekoppelde services** te starten. Selecteer de gateway om de **Gateway** pagina te starten. Op de pagina gateway kunt u de knoop punten zien die zijn gekoppeld aan de gateway. Op de pagina kunt u een knoop punt verwijderen van de gateway.
 
Klik na het verwijderen op **Preview-functies** op dezelfde Azure portal pagina en schakel de preview-functie uit. U hebt de gateway opnieuw ingesteld op één knoop punt GA (algemene Beschik baarheid).


## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende artikelen:
- [Data Management Gateway](data-factory-data-management-gateway.md) -biedt een gedetailleerd overzicht van de gateway.
- [Gegevens verplaatsen tussen on-premises en Cloud gegevens archieven](data-factory-move-data-between-onprem-and-cloud.md) : bevat een overzicht met stapsgewijze instructies voor het gebruik van een gateway met een enkel knoop punt. 
