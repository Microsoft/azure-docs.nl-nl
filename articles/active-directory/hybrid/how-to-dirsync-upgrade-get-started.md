---
title: 'Azure AD Connect: upgraden van DirSync | Microsoft Docs'
description: Kom meer te weten over het upgraden van DirSync naar Azure AD Connect. In deze artikelen worden de stappen beschreven voor het upgraden van DirSync naar Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8aa45294de4ef644c20ef66b7163706dca9759d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996522"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: upgraden van DirSync
Azure AD Connect is de opvolger van DirSync. In dit onderwerp vindt u de manieren voor het upgraden van DirSync. Deze stappen werken niet voor een upgrade van een andere versie van Azure AD Connect of Azure AD Sync.

Zorg ervoor dat u, voordat u begint met de installatie van Azure AD Connect, [Azure AD Connect downloadt](https://go.microsoft.com/fwlink/?LinkId=615771) en de vereiste stappen in [Azure AD Connect: Hardware and prerequisites](how-to-connect-install-prerequisites.md) voltooit. In het bijzonder raden we u aan het volgende te lezen, omdat deze gebieden verschillen van DirSync:

* De vereiste versie van .NET en Power shell. Nieuwere versies zijn vereist op de server voor DirSync.
* De proxyserverconfiguratie. Als u een proxyserver gebruikt om verbinding te maken met internet, moet deze instelling worden geconfigureerd voordat u een upgrade uitvoert. DirSync gebruikte altijd de proxyserver die was geconfigureerd voor de gebruiker die de installatie uitvoerde, maar Azure AD Connect gebruikt de instellingen van de machine.
* De URL's die moeten zijn geopend in de proxyserver. Voor algemene scenario's die ook worden ondersteund door DirSync, zijn de vereisten hetzelfde. Als u gebruik wilt maken van een van de nieuwe functies van Azure AD Connect, moeten er een aantal nieuwe URL's worden geopend.

> [!NOTE]
> Zodra u de nieuwe Azure AD Connect-server hebt ingeschakeld om de wijzigingen te synchroniseren met Azure AD, mag u geen gebruikmaken van DirSync of Azure AD Sync. Downgrade van Azure AD Connect naar verouderde clients, waaronder DirSync en Azure AD Sync wordt niet ondersteund en kan leiden tot problemen zoals gegevens verlies in azure AD.

Als u de upgrade niet vanaf DirSync wilt uitvoeren, zie dan verwante documentatie voor andere scenario's.

## <a name="upgrade-from-dirsync"></a>Upgrade van DirSync
Er zijn verschillende opties voor de upgrade, afhankelijk van uw huidige DirSync-implementatie. Als de verwachte tijd voor de upgrade korter is dan drie uur, wordt het aanbevolen om een in-place upgrade uit te voeren. Als de verwachte tijd voor de upgrade langer is dan drie uur, wordt het aanbevolen om een parallelle implementatie uit te voeren op een andere server. Als u meer dan 50.000 objecten hebt, schatten we dat de upgrade langer dan drie uur duurt.

| Scenario |
| --- |
| [In-place upgrade](#in-place-upgrade) |
| [Parallelle implementatie](#parallel-deployment) |

> [!NOTE]
> Wanneer u van plan bent een upgrade uit te voeren van DirSync naar Azure AD Connect, moet u niet zelf DirSync verwijderen voorafgaand aan de upgrade. Azure AD Connect zal de configuratie van DirSync lezen en migreren, en vervolgens verwijderen na het inspecteren van de server.

**In-place upgrade**  
De verwachte tijd om de upgrade te voltooien wordt door de wizard weergegeven. Deze schatting is gebaseerd op de veronderstelling dat het uitvoeren van een upgrade voor een database met 50.000 objecten (gebruikers, contactpersonen en groepen) drie uur duurt. Als het aantal objecten in uw database minder dan 50.000 is, beveelt Azure AD Connect een in-place upgrade aan. Als u besluit om door te gaan, worden uw huidige instellingen automatisch toegepast tijdens de upgrade en hervat uw server automatisch de actieve synchronisatie.

Als u een configuratiemigratie en een parallelle implementatie wilt uitvoeren, kunt u de aanbeveling voor een in-place upgrade negeren. U kunt bijvoorbeeld van het moment gebruikmaken voor het vernieuwen van de hardware en het besturingssysteem. Zie de sectie [Parallelle implementatie](#parallel-deployment) voor meer informatie.

**Parallelle implementatie**  
Als u meer dan 50.000 objecten hebt, wordt een parallelle implementatie aanbevolen. Deze implementatie voorkomt dat uw gebruikers last krijgen van een vertraagde service. Het installatieprogramma van Azure AD Connect probeert de downtime voor de upgrade in te schatten, maar als u DirSync in het verleden hebt geüpgraded, is uw eigen ervaring waarschijnlijk de beste richtlijn.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Ondersteunde configuraties voor DirSync die worden bijgewerkt
In de bijgewerkte versie van DirSync worden de volgende configuratiewijzigingen ondersteund:

* Domein en OE filteren
* Alternatief id (UPN)
* Wachtwoordsynchronisatie en Exchange hybrid uitwisselen
* Uw forest/domein- en Azure AD-instellingen
* Filteren op basis van gebruikerskenmerken

De volgende wijziging kan niet worden bijgewerkt. Als u deze configuratie hebt, wordt de upgrade geblokkeerd:

* Niet-ondersteunde DirSync-wijzigingen: bijvoorbeeld verwijderde kenmerken en het gebruik van een aangepaste extensie-DLL

![Upgrade geblokkeerd](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

In dit soort gevallen wordt het installeren van een nieuwe Azure AD Connect-server in de [faseringsmodus](how-to-connect-sync-staging-server.md) en het controleren van de oude DirSync en nieuwe Azure AD Connect-configuratie aanbevolen. Pas eventuele wijzigingen aan met behulp van aangepaste configuratie, zoals beschreven in [Aangepaste configuratie Azure AD Connect-synchronisatie](how-to-connect-sync-whatis.md).

De wachtwoorden die door DirSync worden gebruikt voor de serviceaccounts kunnen niet worden opgehaald en worden niet gemigreerd. Deze wachtwoorden worden opnieuw ingesteld tijdens de upgrade.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Belangrijke stappen voor het upgraden van DirSync naar Azure AD Connect
1. Welkom bij Azure AD Connect
2. Analyse van de huidige DirSync-configuratie
3. Azure AD globaal beheerderswachtwoord ophalen
4. Referenties voor een enterprise-beheerdersaccount ophalen (alleen gebruikt tijdens de installatie van Azure AD Connect)
5. Installatie van Azure AD Connect
   * DirSync verwijderen (of tijdelijk uitschakelen)
   * Azure AD Connect installeren
   * Optioneel beginnen met synchroniseren

Extra stappen zijn vereist wanneer:

* U momenteel de volledige SQL Server gebruikt (lokaal of extern)
* U meer dan 50.000 objecten gereed hebt voor synchronisatie

## <a name="in-place-upgrade"></a>In-place upgrade
1. Start het Azure AD Connect-installatieprogramma (MSI) op.
2. Lees en ga akkoord met de licentievoorwaarden en de privacyverklaring.  
   ![Welkom bij Azure AD](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Klik op Volgende om te beginnen met de analyse van uw huidige DirSync-installatie.  
   ![Analyse van de huidige Directory Sync-installatie](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. Als de analyse is voltooid, verschijnen de aanbevelingen voor de vervolgacties.  
   * Als u SQL Server Express gebruikt en minder dan 50.000 objecten hebt, wordt het volgende venster weergegeven:  
     ![Analyse voltooid, gereed voor upgrade van DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * Als u een volledige SQL Server voor DirSync gebruikt, ziet u in plaats daarvan deze pagina:  
     ![scherm opname van de bestaande SQL database-server die wordt gebruikt.](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     De informatie over de huidige SQL Server-databaseserver die wordt gebruikt door DirSync wordt weergegeven. Maak, indien nodig, de juiste aanpassingen. Klik op **Volgende** om door te gaan met de installatie.
   * Als u meer dan 50.000 objecten hebt, ziet u dit scherm:  
     ![Scherm opname van het scherm dat wordt weer gegeven wanneer u meer dan 50.000 objecten hebt om bij te werken.](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Klik om door te gaan met een in-place upgrade op het selectievakje naast dit bericht : **Doorgaan met het uitvoeren van de upgrade van DirSync op deze computer.**
     Voor het uitvoeren van een [parallelle implementatie](#parallel-deployment) dient u de DirSync-configuratie-instellingen te exporteren en de configuratie naar de nieuwe server te verplaatsen.
5. Geef het wachtwoord op voor het account dat u momenteel gebruikt om verbinding te maken met Azure AD. Dit moet het account zijn dat momenteel wordt gebruikt door DirSync.  
   ![Voer uw Azure AD-referenties in](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Als u een foutbericht krijgt en problemen met de connectiviteit heeft, raadpleeg dan [Connectiviteitsproblemen oplossen](tshoot-connect-connectivity.md).
6. Geef een enterprise-beheerdersaccount op voor Active Directory.  
   ![Voer uw ADDS-referenties in](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. U kunt nu starten met de configuratie. Wanneer u op **Upgrade** klikt, wordt DirSync verwijderd en Azure AD Connect geconfigureerd. De synchronisatie wordt gestart.  
   ![Klaar om te configureren](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Nadat de installatie is voltooid, dient u zich af te melden en weer aan te melden bij Windows vóór u Synchronization Service Manager of Synchronization Rule Editor gaat gebruiken of andere configuratiewijzigingen gaat maken.

## <a name="parallel-deployment"></a>Parallelle implementatie
### <a name="export-the-dirsync-configuration"></a>De DirSync-configuratie exporteren
**Parallelle implementatie met meer dan 50.000 objecten**

Als u meer dan 50.000 objecten hebt, raadt de Azure AD Connect-installatie een parallelle implementatie aan.

Er wordt een venster met de volgende strekking weergegeven:  
![Analyse voltooid](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Als u wilt doorgaan met de parallelle implementatie, moet u de volgende stappen uitvoeren:

* Klik op de knop **Instellingen exporteren**. Als u Azure AD Connect op een afzonderlijke server installeert, worden deze instellingen gemigreerd van uw huidige DirSync naar de nieuwe Azure AD Connect-installatie.

Wanneer uw instellingen zijn geëxporteerd, kunt u de Azure AD Connect-wizard afsluiten op de DirSync-server. Ga door met de volgende stap om Azure AD Connect op een afzonderlijke server te installeren

**Parallelle implementatie met minder dan 50.000 objecten**

Als u minder dan 50.000 objecten hebt, maar nog steeds een parallelle implementatie wilt uitvoeren, doet u het volgende:

1. Start het Azure AD Connect-installatieprogramma (MSI) op.
2. Als u het venster **Welkom bij Azure AD Connect** ziet, sluit u de installatiewizard door te klikken op 'X' in de rechterbovenhoek van het venster.
3. Open een opdrachtprompt.
4. Voer de volgende opdracht uit vanaf de locatie waar Azure AD Connect is geïnstalleerd (standaard: C:\Program Files\Microsoft Azure Active Directory Connect): `AzureADConnect.exe /ForceExport`.
5. Klik op de knop **Instellingen exporteren**. Als u Azure AD Connect op een afzonderlijke server installeert, worden deze instellingen gemigreerd van uw huidige DirSync naar de nieuwe Azure AD Connect-installatie.

![Scherm afbeelding met de export instellingen optie voor het migreren van uw instellingen naar de nieuwe Azure AD Connect-installatie.](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

Wanneer uw instellingen zijn geëxporteerd, kunt u de Azure AD Connect-wizard afsluiten op de DirSync-server. Ga door met de volgende stap om Azure AD Connect op een afzonderlijke server te installeren.

### <a name="install-azure-ad-connect-on-separate-server"></a>Azure AD Connect op een afzonderlijke server installeren
Wanneer u Azure AD Connect installeert op een nieuwe server, gaat deze ervan uit dat u een schone installatie van Azure AD Connect wilt uitvoeren. Omdat u de DirSync-configuratie wilt gebruiken, zult u nog een paar extra stappen moeten uitvoeren:

1. Start het Azure AD Connect-installatieprogramma (MSI) op.
2. Als u het venster **Welkom bij Azure AD Connect** ziet, sluit u de installatiewizard door te klikken op 'X' in de rechterbovenhoek van het venster.
3. Open een opdrachtprompt.
4. Voer de volgende opdracht uit vanaf de locatie waar Azure AD Connect is geïnstalleerd (Standaard: C:\Program Files\Microsoft Azure Active Directory Connect)`AzureADConnect.exe /migrate`
   De Azure AD Connect-installatiewizard wordt gestart en geeft het volgende scherm weer:  
   ![Scherm afbeelding die laat zien waar het instellingen bestand moet worden geïmporteerd wanneer u een upgrade uitvoert.](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. Selecteer het instellingenbestand dat is geëxporteerd vanuit uw DirSync-installatie.
6. Configureer de geavanceerde opties, inclusief:
   * Een aangepaste installatielocatie voor Azure AD Connect.
   * Een bestaand exemplaar van SQL Server (standaard: Azure AD Connect installeert SQL Server 2012 Express). Gebruik niet hetzelfde database-exemplaar als die van uw DirSync-server.
   * Een serviceaccount gebruikt voor de verbinding met SQL Server (als de SQL Server-database extern is, moet dit account een domeinserviceaccount zijn).
     Deze opties zijn te zien op dit scherm:  
     ![Scherm afbeelding met de voorschot configuratie opties voor het upgraden van DirSync.](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. Klik op **Volgende**.
8. Laat op de pagina **Gereed voor configuratie** het vakje **Start het synchronisatieproces zodra de configuratie is voltooid** aangevinkt. De server is nu in de [faseringsmodus](how-to-connect-sync-staging-server.md) dus wijzigingen worden niet geëxporteerd naar Azure AD.
9. Klik op **Install**.
10. Nadat de installatie is voltooid, dient u zich af te melden en weer aan te melden bij Windows vóór u Synchronization Service Manager of Synchronization Rule Editor gaat gebruiken of andere configuratiewijzigingen gaat maken.

> [!NOTE]
> De synchronisatie van Windows Server Active Directory en Azure Active Directory wordt gestart, maar er worden geen wijzigingen geëxporteerd naar Azure AD. Slechts één synchronisatieprogramma tegelijk kan actief wijzigingen exporteren. Deze status wordt de [faseringsmodus](how-to-connect-sync-staging-server.md) genoemd.

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Controleren of Azure AD Connect gereed is voor de synchronisatie
Om te controleren of Azure AD Connect gereed is om het over te nemen van DirSync dient u **Synchronization Service Manager** in de groep **Azure AD Connect** te openen vanuit het startmenu.

Ga in de toepassing naar het tabblad **bewerkingen** . Controleer op dit tabblad of de volgende bewerkingen zijn voltooid:

* Importeren op de AD-connector
* Importeren op de Azure AD-connector
* Volledige synchronisatie van de AD-Connector
* Volledige synchronisatie van de Azure AD-Connector

![Importeren en synchroniseren zijn voltooid](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Controleer het resultaat van deze bewerkingen en zorg ervoor dat er geen fouten zijn.

Als u de wijzigingen die op het punt staan geëxporteerd te worden naar Azure AD wilt bekijken en controleren, lees dan hier hoe u de configuratie onder [faseringsmodus](how-to-connect-sync-staging-server.md) kunt controleren. Voer de vereiste wijzigingen door in de configuratie zodat alles klopt.

U kunt van DirSync overschakelen naar Azure AD wanneer u deze stappen hebt uitgevoerd en blij bent met het resultaat.

### <a name="uninstall-dirsync-old-server"></a>DirSync verwijderen (oude server)
* Zoek in **Programma's en onderdelen** naar **Windows Azure Active Directory-synchronisatie**
* **Windows Azure Active Directory-synchronisatie** verwijderen
* Het verwijderen kan tot 15 minuten duren.

Als u DirSync liever later verwijdert, kunt u ook tijdelijk de server afsluiten of de service uitschakelen. Als er iets verkeerd gaat, kunt u het opnieuw inschakelen met deze methode. Het is echter niet waarschijnlijk dat de volgende stap mislukt, dus dit is waarschijnlijk niet nodig.

Wanneer DirSync is verwijderd of uitgeschakeld, is er geen actieve server meer die naar Azure AD exporteert. De volgende stap voor het inschakelen van Azure AD Connect moet worden voltooid voordat de wijzigingen in uw on-premises Active Directory gesynchroniseerd blijven worden naar Azure AD.

### <a name="enable-azure-ad-connect-new-server"></a>Azure AD Connect inschakelen (nieuwe server)
Na de installatie, biedt Azure AD Connect u de mogelijkheid om extra configuratiewijzigingen door te voeren. Start **Azure AD Connect** op vanuit het startmenu of vanuit de snelkoppeling op het bureaublad. Zorg ervoor dat u niet nogmaals het installatieprogramma MSI opstart.

U ziet nu het volgende:  
![Extra taken](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* Selecteer **Faseringsmodus configureren**.
* Schakel fasering uit door het vinkje weg te halen bij het selectievakje **Faseringsmodus ingeschakeld**.

![Scherm afbeelding met de optie voor het inschakelen van de faserings modus.](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* Klik op de knop **volgende**
* Klik op de bevestigingspagina op de knop **Installeren**.

Azure AD Connect is nu uw actieve server. Schakel niet over op de bestaande DirSync-server.

## <a name="next-steps"></a>Volgende stappen
Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](how-to-connect-post-installation.md).

Kom meer te weten over deze functies, die tijdens de installatie zijn ingeschakeld: [Automatische upgrade](how-to-connect-install-automatic-upgrade.md), [Onopzettelijk verwijderen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](how-to-connect-health-sync.md).

Lees meer over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](how-to-connect-sync-feature-scheduler.md).

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
