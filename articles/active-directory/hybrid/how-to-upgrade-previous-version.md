---
title: 'Azure AD Connect: upgraden van een vorige versie | Microsoft Docs'
description: Hierin worden de verschillende methoden beschreven voor het uitvoeren van een upgrade naar de nieuwste versie van Azure Active Directory Connect, met inbegrip van een in-place upgrade en een swing migratie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36b7fce2e2ccb6f331e42e8052ef4fb75d35e831
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98729987"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: upgraden van een vorige naar de meest recente versie
In dit onderwerp worden de verschillende methoden beschreven die u kunt gebruiken om uw Azure Active Directory (Azure AD) Connect-installatie te upgraden naar de nieuwste versie.  U kunt ook de stappen in de sectie [Swing Migration](#swing-migration) gebruiken wanneer u een substantiële configuratie wijziging aanbrengt.

>[!NOTE]
> Het is belang rijk dat u uw servers up-to-date hebt met de nieuwste releases van Azure AD Connect. Er worden voortdurend upgrades naar AADConnect gemaakt en deze upgrades bevatten oplossingen voor beveiligings problemen en bugs, evenals verbeteringen in de prestaties en schaal baarheid. Als u wilt weten wat de nieuwste versie is, en om te zien welke wijzigingen zijn aangebracht tussen versies, raadpleegt u de [release-versie geschiedenis](./reference-connect-version-history.md)

>[!NOTE]
> Er wordt momenteel een upgrade van een versie van Azure AD Connect naar de huidige versie ondersteund. In-place Upgrades van DirSync of ADSync worden niet ondersteund en een swing migratie is vereist.  Als u een upgrade wilt uitvoeren van DirSync, raadpleegt u [upgrade van het hulp programma Azure AD Sync (DirSync)](how-to-dirsync-upgrade-get-started.md) of de sectie [Swing Migration](#swing-migration) (Engelstalig).  </br>In de praktijk kunnen klanten op extreem oude versies problemen ondervinden die niet rechtstreeks verband houden met Azure AD Connect. Servers die gedurende enkele jaren zijn geproduceerd, hebben doorgaans verschillende patches toegepast en kunnen niet allemaal worden verwerkt.  In het algemeen moeten klanten die in 12-18 maanden niet zijn bijgewerkt, een swing-upgrade overwegen, omdat dit de meest conservatieve en minst Risk ante optie is.

Als u een upgrade wilt uitvoeren van DirSync, raadpleegt u de [upgrade van het Azure AD-synchronisatie hulpprogramma (DirSync)](how-to-dirsync-upgrade-get-started.md) in plaats daarvan.

Er zijn een aantal verschillende strategieën die u kunt gebruiken om Azure AD Connect bij te werken.

| Methode | Beschrijving |
| --- | --- |
| [Automatische upgrade](how-to-connect-install-automatic-upgrade.md) |Dit is de eenvoudigste methode voor klanten met een snelle installatie. |
| [In-place upgrade](#in-place-upgrade) |Als u één server hebt, kunt u de installatie in-place op dezelfde server bijwerken. |
| [Swingmigratie](#swing-migration) |Met twee servers kunt u een van de servers voorbereiden met de nieuwe release of configuratie en de actieve server wijzigen wanneer u klaar bent. |

Zie voor informatie over machtigingen de [machtigingen die vereist zijn voor een upgrade](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Nadat u de nieuwe Azure AD Connect-server hebt ingeschakeld om de wijzigingen te synchroniseren met Azure AD, moet u niet teruggaan naar het gebruik van DirSync of Azure AD Sync. Downgrade van Azure AD Connect naar verouderde clients, waaronder DirSync en Azure AD Sync, wordt niet ondersteund en kan leiden tot problemen zoals gegevens verlies in azure AD.

## <a name="in-place-upgrade"></a>In-place upgrade
Een in-place upgrade werkt om van Azure AD Sync of Azure AD Connect te verplaatsen. Het werkt niet voor het verplaatsen van DirSync of voor een oplossing met de Forefront Identity Manager (FIM) + Azure AD-connector.

Deze methode verdient de voor keur wanneer u één server hebt en minder dan ongeveer 100.000 objecten. Als er wijzigingen zijn aangebracht in de out-of-Box-synchronisatie regels, worden er na de upgrade een volledige import en volledige synchronisatie uitgevoerd. Deze methode zorgt ervoor dat de nieuwe configuratie wordt toegepast op alle bestaande objecten in het systeem. Deze uitvoering kan enkele uren duren, afhankelijk van het aantal objecten dat zich binnen het bereik van de synchronisatie-engine bevindt. De normale Delta synchronisatie planner (die standaard elke 30 minuten synchroniseert) wordt onderbroken, maar de wachtwoord synchronisatie wordt voortgezet. U kunt overwegen om de in-place upgrade te doen tijdens een weekend. Als er geen wijzigingen zijn in de out-of-box-configuratie met de nieuwe Azure AD Connect versie, wordt in plaats daarvan een normale Delta-import/-synchronisatie gestart.  
![In-place upgrade](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Als u wijzigingen hebt aangebracht in de out-of-Box-synchronisatie regels, worden deze regels weer ingesteld op de standaard configuratie voor de upgrade. Om ervoor te zorgen dat uw configuratie wordt bewaard tussen upgrades, moet u ervoor zorgen dat u wijzigingen aanbrengt zoals beschreven in [Aanbevolen procedures voor het wijzigen van de standaard configuratie](how-to-connect-sync-best-practices-changing-default-configuration.md).

Tijdens in-place upgrade zijn mogelijk wijzigingen aangebracht die vereisen dat specifieke synchronisatie activiteiten (inclusief volledige import stap en volledige synchronisatie stap) worden uitgevoerd nadat de upgrade is voltooid. Als u dergelijke activiteiten wilt uitstellen, raadpleegt u de sectie de [volledige synchronisatie uitstellen na de upgrade](#how-to-defer-full-synchronization-after-upgrade).

Als u Azure AD Connect met een niet-standaard connector (bijvoorbeeld algemene LDAP-connector en algemene SQL-connector) gebruikt, moet u de bijbehorende connector configuratie in de [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) na in-place upgrade vernieuwen. Raadpleeg voor meer informatie over het vernieuwen van de connector configuratie de sectie artikel [release connector versie geschiedenis-probleem oplossing](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Als u de configuratie niet vernieuwt, zullen de stappen voor importeren en exporteren niet goed werken voor de connector. U ontvangt de volgende fout in het gebeurtenis logboek van de toepassing met het bericht *assembly-versie in Aad-connector configuratie (x. x. xxx. x) is vroeger dan de daad werkelijke versie (x. x. xxx. x) van ' C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll '.*

## <a name="swing-migration"></a>Swingmigratie
Als u een complexe implementatie of veel objecten hebt, is het wellicht niet praktisch om een in-place upgrade uit te voeren op het live-systeem. Voor sommige klanten kan dit proces meerdere dagen duren, en gedurende deze periode worden er geen wijzigingen in de Delta verwerkt. U kunt deze methode ook gebruiken wanneer u van plan bent om belang rijke wijzigingen aan te brengen in de configuratie en u deze wilt uitproberen voordat ze naar de cloud worden gepusht.

De aanbevolen methode voor deze scenario's is het gebruik van een swing migratie. U hebt mini maal twee servers nodig: één actieve server en één staging-server. De actieve server (weer gegeven met effen blauwe lijnen in de volgende afbeelding) is verantwoordelijk voor de actieve productie belasting. De staging-server (weer gegeven met streepjes paarse lijnen) wordt voor bereid met de nieuwe versie of configuratie. Wanneer het volledig klaar is, wordt deze server actief gemaakt. De vorige actieve server, waarop nu de oude versie of configuratie is geïnstalleerd, wordt in de staging-server gemaakt en bijgewerkt.

De twee servers kunnen verschillende versies gebruiken. De actieve server die u van plan bent uit te nemen, kan bijvoorbeeld gebruikmaken van Azure AD Sync, en de nieuwe staging-server kan gebruikmaken van Azure AD Connect. Als u swing migratie gebruikt voor het ontwikkelen van een nieuwe configuratie, is het een goed idee om dezelfde versies op de twee servers te hebben.  
![Staging-server](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Sommige klanten geven de voor keur aan drie of vier servers voor dit scenario. Wanneer de staging-server is bijgewerkt, hebt u geen back-upserver voor [herstel na nood gevallen](how-to-connect-sync-staging-server.md#disaster-recovery). Met drie of vier servers kunt u één set primaire/stand-by-servers voorbereiden met de nieuwe versie, zodat er altijd een staging-server is die klaar is om over te nemen.

Deze stappen werken ook om van Azure AD Sync of een oplossing met FIM + Azure AD connector te verplaatsen. Deze stappen werken niet voor DirSync, maar dezelfde Swing-migratie methode (ook wel parallelle implementatie genoemd) met stappen voor DirSync bevindt zich in [Upgrade Azure Active Directory Sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Een swing migratie gebruiken om bij te werken
1. Als u Azure AD Connect op beide servers gebruikt en u alleen een configuratie wijziging wilt door voeren, moet u ervoor zorgen dat de actieve server en de faserings server beide dezelfde versie gebruiken. Hierdoor is het eenvoudiger om de verschillen later te vergelijken. Als u een upgrade uitvoert vanaf Azure AD Sync, hebben deze servers verschillende versies. Als u een upgrade uitvoert van een oudere versie van Azure AD Connect, is het een goed idee om te beginnen met de twee servers die dezelfde versie gebruiken, maar dit is niet vereist.
2. Als u een aangepaste configuratie hebt gemaakt en uw staging-server het niet heeft, volgt u de stappen onder [een aangepaste configuratie van de actieve server naar de staging-server verplaatsen](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Als u een upgrade uitvoert van een eerdere versie van Azure AD Connect, moet u de staging-server bijwerken naar de nieuwste versie. Als u overstapt van Azure AD Sync, installeert u Azure AD Connect op uw staging-server.
4. De synchronisatie-engine volledige import en volledige synchronisatie op uw staging-server laten uitvoeren.
5. Controleer of de nieuwe configuratie geen onverwachte wijzigingen heeft veroorzaakt door de stappen onder ' controleren ' te gebruiken in [Controleer de configuratie van een server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Als dat niet zo is, corrigeert u het, voert u de import-en synchronisatie uit en controleert u de gegevens totdat deze goed worden weer gegeven door de stappen te volgen.
6. Zet de staging-server om in de actieve server. Dit is de laatste stap ' switch Active Server ' in [Controleer de configuratie van een server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Als u Azure AD Connect upgradet, voert u een upgrade uit van de server die nu in de faserings modus is uitgevoerd naar de nieuwste versie. Volg dezelfde stappen als voordat u de bijgewerkte gegevens en configuratie ophaalt. Als u een upgrade hebt uitgevoerd vanaf Azure AD Sync, kunt u de oude server nu uitschakelen en buiten gebruik stellen.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Een aangepaste configuratie van de actieve server naar de staging-server verplaatsen
Als u configuratie wijzigingen hebt aangebracht aan de actieve server, moet u ervoor zorgen dat dezelfde wijzigingen worden toegepast op de staging-server. U kunt de [Azure AD Connect-configuratie Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter)gebruiken om u te helpen bij het verplaatsen.

U kunt de aangepaste synchronisatie regels die u hebt gemaakt, verplaatsen met behulp van Power shell. U moet andere wijzigingen op dezelfde manier op beide systemen Toep assen en u kunt de wijzigingen niet migreren. De [configuratie documentatie](https://github.com/Microsoft/AADConnectConfigDocumenter) kan u helpen bij het vergelijken van de twee systemen om er zeker van te zijn dat ze identiek zijn. Het hulp programma kan ook helpen bij het automatiseren van de stappen die in deze sectie worden gevonden.

U moet de volgende dingen op beide servers op dezelfde manier configureren:

* Verbinding met dezelfde forests
* Domein-en OE-filtering
* Dezelfde optionele functies, zoals wachtwoord synchronisatie en wacht woord terugschrijven

**Aangepaste synchronisatie regels verplaatsen**  
Ga als volgt te werk om aangepaste synchronisatie regels te verplaatsen:

1. Open de **Editor voor synchronisatie regels** op de actieve server.
2. Selecteer een aangepaste regel. Klik op **Exporteren**. Hiermee opent u een Klad blok-venster. Sla het tijdelijke bestand op met een PS1-extensie. Dit maakt het een Power shell-script. Kopieer het PS1-bestand naar de staging-server.  
   ![Synchronisatie regel exporteren](./media/how-to-upgrade-previous-version/exportrule.png)
3. De connector-GUID verschilt op de staging-server en u moet deze wijzigen. Als u de GUID wilt ophalen, start u de **Editor voor synchronisatie regels**, selecteert u een van de out-of-Box-regels die overeenkomen met hetzelfde verbonden systeem en klikt u op **exporteren**. Vervang de GUID in uw PS1-bestand door de GUID van de staging-server.
4. Voer het PS1-bestand uit in een Power shell-prompt. Hiermee maakt u de aangepaste synchronisatie regel op de staging-server.
5. Herhaal dit voor alle aangepaste regels.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Volledige synchronisatie uitstellen na een upgrade
Tijdens in-place upgrade zijn mogelijk wijzigingen aangebracht die vereisen dat specifieke synchronisatie activiteiten (inclusief volledige import stap en volledige synchronisatie stap) worden uitgevoerd. Voor de wijzigingen van connector schema moeten de stap voor **volledige import** en out-of-Box-synchronisatie regel zijn vereist voor een **volledige synchronisatie** stap die moet worden uitgevoerd op de betreffende connectors. Tijdens de upgrade bepaalt Azure AD Connect welke synchronisatie activiteiten vereist zijn en worden ze vastgelegd als *onderdrukkingen*. In de volgende synchronisatie cyclus worden deze onderdrukkingen door de synchronisatie planner opgehaald en uitgevoerd. Zodra een onderdrukking is uitgevoerd, wordt deze verwijderd.

Er zijn mogelijk situaties waarin u niet wilt dat deze onderdrukkingen onmiddellijk na de upgrade plaatsvinden. U hebt bijvoorbeeld talloze gesynchroniseerde objecten en wilt deze synchronisaties tappen na kantoor uren doen. Deze onderdrukkingen verwijderen:

1. Schakel tijdens de upgrade de optie het **synchronisatie proces starten wanneer de configuratie is voltooid** **uit** . Hiermee schakelt u de synchronisatie planner uit en voor komt u dat de synchronisatie cyclus automatisch plaatsvindt voordat de onderdrukkingen worden verwijderd.

   ![Scherm afbeelding die het begin van het synchronisatie proces markeert wanneer de configuratie is voltooid en de optie moet worden gewist.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Nadat de upgrade is voltooid, voert u de volgende cmdlet uit om erachter te komen welke onderdrukkingen zijn toegevoegd: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > De onderdrukkingen zijn specifiek voor een connector. In het volgende voor beeld zijn de stap volledige import en volledige synchronisatie toegevoegd aan zowel de on-premises AD-connector als de Azure AD-connector.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Noteer de bestaande onderdrukkingen die zijn toegevoegd.
   
4. Voer de volgende cmdlet uit om de onderdrukkingen voor zowel volledige import als volledige synchronisatie van een wille keurige connector te verwijderen: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Als u de onderdrukkingen op alle connectors wilt verwijderen, voert u het volgende Power shell-script uit:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Voer de volgende cmdlet uit om de scheduler te hervatten: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Vergeet niet om de vereiste synchronisatie stappen zo snel mogelijk uit te voeren. U kunt deze stappen hand matig uitvoeren met de Synchronization Service Manager of de onderdrukkingen opnieuw toevoegen met de Set-ADSyncSchedulerConnectorOverride-cmdlet.

Voer de volgende cmdlet uit om de onderdrukkingen voor zowel volledige import als volledige synchronisatie van een wille keurige connector toe te voegen:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Problemen oplossen
De volgende sectie bevat probleemoplossingen en informatie die u kunt gebruiken als u een probleem ondervindt bij het upgraden van Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Er ontbreekt een Azure Active Directory Connector tijdens de Azure AD Connect-upgrade

Wanneer u Azure AD Connect van een vorige versie bijwerkt, kan aan het begin van de upgrade de volgende fout optreden 

![Fout](./media/how-to-upgrade-previous-version/error1.png)

Deze fout treedt op omdat de Azure Active Directory connector met id, b891884f-051e-4a83-95af-2544101c9083, niet bestaat in de huidige Azure AD Connect configuratie. Als u wilt controleren of dit het geval is, opent u een Power shell-venster, voert u de cmdlet uit `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

De Power shell-cmdlet rapporteert de fout die **het opgegeven ma niet kan worden gevonden**.

De reden hiervoor is dat de huidige Azure AD Connect configuratie niet wordt ondersteund voor een upgrade. 

Als u een nieuwere versie van Azure AD Connect wilt installeren, sluit u de Azure AD Connect wizard, verwijdert u de bestaande Azure AD Connect en voert u een schone installatie van de nieuwere Azure AD Connect uit.



## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).