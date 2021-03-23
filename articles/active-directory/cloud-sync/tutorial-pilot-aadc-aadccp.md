---
title: Zelf studie-pilot Azure AD Connect Cloud Sync voor een bestaand gesynchroniseerd AD-forest
description: Meer informatie over het testen van Cloud synchronisatie voor een test Active Directory forest die al is gesynchroniseerd met behulp van Azure Active Directory (Azure AD) Connect Sync.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50eac71203a94ffb5c7dddc8995b56980c3f8815
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798711"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>Pilot Cloud Sync voor een bestaand gesynchroniseerd AD-forest 

In deze zelf studie wordt u begeleid bij het maken van een Cloud synchronisatie voor een test Active Directory forest dat al is gesynchroniseerd met behulp van Azure Active Directory (Azure AD) Connect Sync.

![Maken](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>Overwegingen
Denk aan de volgende punten voordat u deze zelfstudie uitvoert:
1. Zorg ervoor dat u bekend bent met de basis principes van Cloud synchronisatie. 
2. Zorg dat u Azure AD Connect-synchronisatie versie 1.4.32.0 of hoger uitvoert en de synchronisatieregels hebt geconfigureerd zoals is beschreven. Tijdens het testen verwijdert u een pilot-OE of -groep uit het Azure AD Connect-synchronisatiebereik. Als objecten buiten het bereik worden geplaatst, worden deze objecten in Azure AD verwijderd. Bij gebruikersobjecten worden de objecten in Azure AD zacht verwijderd en kunnen ze worden hersteld. Bij groepsobjecten worden de objecten in Azure AD hard verwijderd en kunnen ze niet worden hersteld. Er is een nieuw koppelingstype beschikbaar in de Azure AD Connect-synchronisatie, waardoor verwijderen niet mogelijk is in een testscenario. 
3. Zorg ervoor dat op de objecten in het test bereik MS-DS-consistencyGUID is ingevuld zodat de Cloud Sync hard overeenkomt met de objecten. 

   > [!NOTE]
   > Standaard vult Azure AD Connect-synchronisatie geen *ms-ds-consistencyGUID* voor groepsobjecten.

4. Dit is een geavanceerd scenario. Zorg dat u de stappen in deze zelfstudie nauwkeurig volgt.

## <a name="prerequisites"></a>Vereisten
Dit zijn de vereisten voor het voltooien van deze zelfstudie
- Een testomgeving met Azure AD Connect Sync versie 1.4.32.0 of hoger
- Een OE of groep die is gesynchroniseerd en kan worden gebruikt als pilot. We raden u aan te beginnen met een kleine set objecten.
- Een server met Windows Server 2012 R2 of hoger waarop de inrichtingsagent wordt gehost.  Dit magn niet dezelfde server zijn als de Azure AD Connect-server.
- Bronanker voor Azure AD Connect Sync moet *​​objectGuid* of *ms-ds-consistencyGUID* zijn

## <a name="update-azure-ad-connect"></a>Azure AD Connect bijwerken

U moet minimaal [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0 hebben. Voer de stappen in [Azure AD Connect uit om Azure AD Connect Sync bij te werken: Voer een upgrade naar de nieuwste versie uit](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>De planner stoppen
Azure AD Connect Sync synchroniseert wijzigingen die plaatsvinden in uw on-premises adreslijst met behulp van een planner. Als u aangepaste regels wilt wijzigen en toevoegen, schakelt u de planner uit zodat er geen synchronisaties worden uitgevoerd terwijl u hieraan werkt.  Voer de volgende stappen uit:

1.  Open PowerShell met beheerdersrechten op de server waarop Azure AD Connect wordt uitgevoerd.
2.  Voer `Stop-ADSyncSyncCycle` uit.  Druk op Enter.
3.  Voer `Set-ADSyncScheduler -SyncCycleEnabled $false` uit.

>[!NOTE] 
>Als u uw eigen aangepaste planner voor Azure AD Connect Sync gebruikt, schakelt u de planner uit. 

## <a name="create-custom-user-inbound-rule"></a>Een aangepaste regel voor binnenkomende verbindingen voor gebruikers maken

 1. Start de synchronisatie-editor vanuit het toepassingsmenu op het bureaublad, zoals hieronder wordt weergegeven:</br>
 ![Menu voor synchronisatieregeleditor](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>
 
 2. Selecteer **Binnenkomend** in de vervolgkeuzelijst voor Richting en klik op **Nieuwe regel toevoegen**.
 ![Schermafbeelding met het venster 'Uw synchronisatieregels weergeven en beheren' met 'binnenkomend' en de knop nieuwe regel toevoegen geselecteerd.](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>
 
 3. Voer op de pagina **Beschrijving** het volgende in en klik op **Volgende**:

    **Naam:** Geef een beschrijvende naam op voor de regel<br>
    **Beschrijving:** Voer een duidelijke omschrijving in<br>
    **Verbonden systeem:** Kies de AD-connector waarvoor u de aangepaste synchronisatieregel schrijft<br>
    **Type verbonden systeemobject:** Gebruiker<br>
    **Metaverse-objecttype:** Person<br>
    **Type koppeling:** Koppelen<br>
    **Prioriteit:** Geef een waarde op die uniek is in het systeem<br>
    **Tag:** Laat dit leeg<br>
    ![Schermopname met de pagina "Binnenkomende synchronisatieregel maken - Beschrijving" met ingevoerde waarden.](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>
 
 4. Voer op de pagina **Bereikfilter** de OE of beveiligingsgroep in waarop u de pilot wilt baseren.  Als u wilt filteren op OE, voegt u het OE-gedeelte van de DN-naam toe. Deze regel wordt toegepast op alle gebruikers in deze OE.  Als DN eindigt op "OU=CPUsers,DC=contoso,DC=com, voegt u dit filter toe.  Klik op **Volgende**. 

    |Regel|Kenmerk|Operator|Waarde|
    |-----|----|----|-----|
    |Bereik OE|DN|ENDSWITH|DN-naam van de OE.|
    |Bereikgroep||ISMEMBEROF|DN-naam van de beveiligingsgroep.|

    ![Schermopname met de pagina "Regel voor binnenkomende synchronisatie - Bereikfilter" met een bereikfilterwaarde ingevoerd.](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>
 
 5. Klik op de pagina **Regels samenvoegen** op **Volgende**.
 6. Voeg op de pagina **Transformaties** een constante transformatie toe: gebruik True voor het kenmerk cloudNoFlow. Klik op **Add**.
 ![Schermopname met de pagina "Regel voor binnenkomende synchronisatie - Transformaties" met de stroom "Constante transformatie" toegevoegd.](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

Dezelfde stappen moeten worden gevolgd voor alle objecttypen (gebruiker, groep en contactpersoon). Herhaal de stappen per geconfigureerde AD-connector/per AD-forest. 

## <a name="create-custom-user-outbound-rule"></a>Een aangepaste regel voor uitgaande verbindingen voor gebruikers maken

 1. Selecteer **Uitgaand** in de vervolgkeuzelijst voor Richting en klik op **Regel toevoegen**.
 ![Schermopname met de richting "Uitgaand" geselecteerd en de knop "Nieuwe regel toevoegen" uitgelicht.](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>
 
 2. Voer op de pagina **Beschrijving** het volgende in en klik op **Volgende**:

    **Naam:** Geef een beschrijvende naam op voor de regel<br>
    **Beschrijving:** Voer een duidelijke omschrijving in<br>
    **Verbonden systeem:** Kies de Azure AD-connector waarvoor u de aangepaste synchronisatieregel schrijft<br>
    **Type verbonden systeemobject:** Gebruiker<br>
    **Metaverse-objecttype:** Person<br>
    **Type koppeling:** JoinNoFlow<br>
    **Prioriteit:** Geef een waarde op die uniek is in het systeem<br>
    **Tag:** Laat dit leeg<br>
    
    ![Schermopname met de pagina "Beschrijving" met ingevoerde eigenschappen.](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>
 
 3. Kies op de pagina **Bereikfilter** de optie **cloudNoFlow** is gelijk aan **True**. Klik op **Volgende**.
 ![Aangepaste regel](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>
 
 4. Klik op de pagina **Regels samenvoegen** op **Volgende**.
 5. Klik op de pagina **Transformaties** op **toevoegen**.

Dezelfde stappen moeten worden gevolgd voor alle objecttypen (gebruiker, groep en contactpersoon).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>De agent voor Azure AD Connect-inrichting installeren
1. Meld u aan bij de server die u wilt gebruiken met beheerdersmachtigingen van de onderneming.  Als u de zelfstudie [Basic AD en Azure-omgeving](tutorial-basic-ad-azure.md) gebruikt is dit CP1.
2. Download de Azure AD Connect-cloudinrichtingsagent met behulp van de stappen die [hier](how-to-install.md#install-the-agent) worden beschreven.
3. De Azure AD Connect Cloud synchronisatie uitvoeren (AADConnectProvisioningAgent. installer)
3. Ga in het welkomstscherm **akkoord** met de licentievoorwaarden en klik op **Installeren**.</br>
![Schermopname van het startscherm 'Microsoft Azure AD Connect Provisioning Agent' wordt weergegeven.](media/how-to-install/install-1.png)</br>

4. Zodra deze bewerking is voltooid, wordt de configuratiewizard gestart.  Meld u aan met uw globale beheerdersreferenties voor Azure AD.
5. Klik in het scherm **Verbinding maken met Active Directory** op **Directory toevoegen** en meld u aan met uw Active Directory-beheerdersaccount.  Met deze bewerking wordt uw on-premises adreslijst toegevoegd.  Klik op **Volgende**.</br>
![Schermopname van het venster 'Verbinding maken met Active Directory' met een ingevoerde mapwaarde.](media/how-to-install/install-3a.png)</br>

6. Klik in het scherm **Configuratie voltooid** op **Bevestigen**.  Met deze bewerking wordt de agent geregistreerd en opnieuw gestart.</br>
![Schermopname met het scherm Configuratie voltooid met de knop "Bevestigen" geselecteerd.](media/how-to-install/install-4a.png)</br>

7. Zodra deze bewerking is voltooid, ziet u de melding **Uw verificatie is geslaagd.**  U kunt op **Afsluiten** klikken.</br>
![Welkomstscherm](media/how-to-install/install-5.png)</br>
8. Als het welkomstscherm nog steeds wordt weergegeven, klikt u op **Sluiten**.

## <a name="verify-agent-installation"></a>Agentinstallatie verifiëren
Verificatie van de agent vindt plaats in de Azure Portal en op de lokale server waarop de agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent
Voer de volgende stappen uit om te controleren of de agent wordt gedetecteerd door Azure:

1. Meld u aan bij Azure Portal.
2. Selecteer aan de linkerkant **Azure Active Directory**, klik op **Azure AD Connect** en selecteer in het midden **Cloud synchronisatie beheren**.</br>
![Azure-portal](media/how-to-install/install-6.png)</br>

3.  Klik op het scherm **Azure AD Connect Cloud Sync** op **Alle agents controleren**.
![Azure AD-inrichting](media/how-to-install/install-7.png)</br>
 
4. In het scherm **On-premises inrichtingsagents** ziet u de agents die u hebt geïnstalleerd.  Controleer of de relevante agent beschikbaar is en op **Uitgeschakeld** staat.  De agent is standaard uitgeschakeld ![Inrichtingsagents](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Op de lokale server
Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd:

1.  Meld u aan bij de server met een beheerdersaccount
2.  Open **Services** door hiernaartoe te navigeren of via Start/Uitvoeren/Services. msc.
3.  Controleer onder **Services** of **Microsoft Azure AD Connect-agentupdater** en **Microsoft Azure AD Connect-inrichtingsagent** beschikbaar zijn en of de status **Actief** is.
![Services](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Azure AD Connect Cloud synchronisatie configureren
Voer de volgende stappen uit om de inrichting te configureren:

 1. Meld u aan bij de Azure AD-portal.
 2. Klik op **Azure Active Directory**
 3. Klik op **Azure AD Connect**
 4. Selecteer scherm opname van **Cloud synchronisatie beheren** 
  ![ met de koppeling Cloud synchronisatie beheren.](media/how-to-configure/manage-1.png)</br>
 5.  Klik op **nieuwe** 
  ![ scherm afbeelding van Azure AD Connect Cloud synchronisatie met de koppeling ' nieuwe configuratie ' gemarkeerd.](media/tutorial-single-forest/configure-1.png)</br>
 6.  Voer in het configuratiescherm een **E-mailadres voor meldingen** in, zet de selector op **Inschakelen** en klik op **Opslaan**.
 ![Schermopname van het scherm Configureren waarin het e-mailadres voor meldingen is ingevuld en Inschakelen is geselecteerd.](media/tutorial-single-forest/configure-2.png)</br>
 7. Selecteer onder **Configureren** de optie **Alle gebruikers** om het bereik van de configuratieregel te wijzigen.
 ![Schermopname van het scherm Configureren waarin Alle gebruikers naast Bereik van gebruikers is gemarkeerd.](media/how-to-configure/scope-2.png)</br>
 8. Wijzig rechts het bereik voor de specifieke OE die u zojuist hebt gemaakt: "OU=CPUsers,DC=contoso,DC=com".
 ![Schermopname van het scherm Bereik van gebruikers waarin het bereik is gewijzigd in de organisatie-eenheid die u hebt gemaakt.](media/tutorial-existing-forest/scope-2.png)</br>
 9.  Klik op **Gereed** en **Opslaan**.
 10. Het bereik moet nu worden ingesteld op één organisatie-eenheid. 
 ![Schermopname van het scherm Configureren waarin 1 organisatie-eenheid is gemarkeerd naast Bereik van gebruikers.](media/tutorial-existing-forest/scope-3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>Controleren of gebruikers zijn ingericht met Cloud synchronisatie
U gaat nu controleren of de gebruikers die beschikbaar waren in onze on-premises adreslijst, zijn gesynchroniseerd en nu beschikbaar zijn in onze Azure AD-tenant.  Dit synchronisatieproces kan enkele uren duren.  Voer de volgende stappen uit om te controleren of gebruikers zijn ingericht door Cloud synchronisatie:

1. Meld u bij de [Azure Portal](https://portal.azure.com) aan met een account waaraan een Azure-abonnement is gekoppeld.
2. Selecteer links **Azure Active Directory**
3. Klik op **Azure AD Connect**
4. Klik op **Cloud synchronisatie beheren**
5. Klik op de knop **Logboeken**
6. Zoek naar een gebruikers naam om te bevestigen dat de gebruiker is ingericht met Cloud synchronisatie

Daarnaast kunt u controleren of de gebruiker en groep bestaan in Azure AD.

## <a name="start-the-scheduler"></a>De planner starten
Azure AD Connect Sync synchroniseert wijzigingen die plaatsvinden in uw on-premises adreslijst met behulp van een planner. Nu u de regels hebt gewijzigd, kunt u de planner opnieuw starten.  Voer de volgende stappen uit:

1.  Open PowerShell met beheerdersrechten op de server waarop Azure AD Connect wordt uitgevoerd
2.  Voer `Set-ADSyncScheduler -SyncCycleEnabled $true` uit.
3.  Voer `Start-ADSyncSyncCycle` uit.  Druk op Enter.  

>[!NOTE] 
>Als u uw eigen aangepaste planner voor Azure AD Connect Sync gebruikt, schakelt u de planner uit. 

Zodra de planner is ingeschakeld, stopt Azure AD Connect met het exporteren van wijzigingen in objecten met `cloudNoFlow=true` in de metaverse, tenzij er een referentiekenmerk (bijvoorbeeld beheer) wordt bijgewerkt. Als er een update is voor het referentiekenmerk voor het object, wordt het `cloudNoFlow`-signaal door Azure AD Connect genegeerd en worden alle updates voor het object geëxporteerd.

## <a name="something-went-wrong"></a>Er is iets fout gegaan
Als de pilot niet werkt zoals verwacht, kunt u teruggaan naar de Azure AD Connect Sync-instellingen door de volgende stappen uit te voeren:
1.  De inrichtingsconfiguratie in de Azure Portal uitschakelen. 
2.  Schakel alle aangepaste synchronisatieregels die voor cloudinrichting zijn gemaakt, uit met het hulpprogramma voor de synchronisatieregeleditor. Door de regels uit te schakelen, worden alle connectors volledig gesynchroniseerd.



## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)

