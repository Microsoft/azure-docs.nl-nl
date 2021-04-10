---
title: Problemen met wachtwoord-hash-synchronisatie met Azure AD Connect synchronisatie oplossen | Microsoft Docs
description: Dit artikel bevat informatie over het oplossen van problemen met wachtwoord-hash-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c6ec162ceb51c3bf19be42219b054d8371ff221
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247349"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Problemen met wachtwoord-hashsynchronisatie oplossen met Azure AD Connect-synchronisatie

Dit onderwerp bevat stappen voor het oplossen van problemen met wachtwoord-hash-synchronisatie. Als wacht woorden niet op de verwachte manier worden gesynchroniseerd, kan dit een subset van gebruikers of voor alle gebruikers zijn.

Voor Azure Active Directory (Azure AD) Connect-implementatie met versie 1.1.614.0 of later gebruikt u de taak voor probleem oplossing in de wizard om problemen met de wachtwoord-hash-synchronisatie op te lossen:

* Als u een probleem hebt waarbij er geen wacht woorden worden gesynchroniseerd, raadpleegt u de sectie [geen wacht woorden worden gesynchroniseerd: problemen oplossen met behulp van het gedeelte taak voor probleem oplossing](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) .

* Als u een probleem met afzonderlijke objecten hebt, raadpleegt u het [ene object heeft geen wacht woorden synchroniseren: problemen oplossen met behulp van de taak sectie probleem oplossing](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) .

Voor de implementatie met versie 1.1.524.0 of hoger is er een diagnostische cmdlet die u kunt gebruiken om problemen met de wachtwoord-hash-synchronisatie op te lossen:

* Als u een probleem hebt waarbij er geen wacht woorden worden gesynchroniseerd, raadpleegt u de sectie [geen wacht woorden synchroniseren: problemen oplossen met behulp van het gedeelte diagnostische cmdlet](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) .

* Als u een probleem met afzonderlijke objecten hebt, raadpleegt u het [ene object heeft geen wacht woorden synchroniseren: problemen oplossen met behulp van de sectie diagnostische cmdlet](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) .

Voor oudere versies van Azure AD Connect-implementatie:

* Als u een probleem hebt waarbij er geen wacht woorden worden gesynchroniseerd, raadpleegt u de sectie [geen woorden synchroniseren: hand matige stappen voor probleem oplossing](#no-passwords-are-synchronized-manual-troubleshooting-steps) .

* Als u een probleem met afzonderlijke objecten hebt, raadpleegt u het [ene object heeft geen wacht woorden synchroniseren: het gedeelte hand matige stappen voor probleem oplossing](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) .



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Er zijn geen wacht woorden gesynchroniseerd: problemen oplossen met behulp van de taak voor probleem oplossing

U kunt de taak voor het oplossen van problemen gebruiken om erachter te komen waarom er geen wacht woorden worden gesynchroniseerd.

> [!NOTE]
> De taak voor probleem oplossing is alleen beschikbaar voor Azure AD Connect versie 1.1.614.0 of hoger.

### <a name="run-the-troubleshooting-task"></a>De taak voor probleem oplossing uitvoeren

Problemen oplossen waarbij er geen wacht woorden worden gesynchroniseerd:

1. Open een nieuwe Windows Power shell-sessie op uw Azure AD Connect-server met de optie **als administrator uitvoeren** .

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` of `Set-ExecutionPolicy Unrestricted` .

3. Start de wizard Azure AD Connect.

4. Ga naar de pagina **extra taken** , selecteer **problemen oplossen** en klik op **volgende**.

5. Klik op de pagina probleem oplossing op **starten** om het menu probleem oplossing in Power shell te starten.

6. Selecteer in het hoofd menu de optie **problemen met wachtwoord-hash-synchronisatie oplossen**.

7. Selecteer in het submenu de optie **wacht woord-hash synchronisatie werkt helemaal niet**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Inzicht in de resultaten van de taak voor het oplossen van problemen

De taak voor het oplossen van problemen voert de volgende controles uit:

* Hiermee wordt gecontroleerd of de functie voor wachtwoord hash-synchronisatie is ingeschakeld voor uw Azure AD-Tenant.

* Hiermee wordt gevalideerd of de Azure AD Connect-server zich niet in de faserings modus bevindt.

* Voor elke bestaande on-premises Active Directory connector (die overeenkomt met een bestaand Active Directory forest):

   * Hiermee wordt gecontroleerd of de functie voor wachtwoord hash-synchronisatie is ingeschakeld.
   
   * Zoekt naar gebeurtenissen voor wachtwoord-hash-heartbeat in de gebeurtenis logboeken van de Windows-toepassing.

   * Voor elk Active Directory domein onder de on-premises Active Directory-connector:

      * Valideert of het domein bereikbaar is vanaf de Azure AD Connect-server.

      * Valideert dat de Active Directory Domain Services (AD DS) accounts die worden gebruikt door de on-premises Active Directory connector, de juiste gebruikers naam, het wacht woord en de vereiste machtigingen voor de synchronisatie van hashes voor wacht woorden hebben.

In het volgende diagram ziet u de resultaten van de cmdlet voor een on-premises Active Directory topologie met één domein:

![Diagnostische uitvoer voor wachtwoord-hash-synchronisatie](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

In de rest van deze sectie worden specifieke resultaten beschreven die worden geretourneerd door de taak en de bijbehorende problemen.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>de functie voor synchronisatie van wacht woord-hash is niet ingeschakeld

Als u de synchronisatie van wacht woord-hashes niet hebt ingeschakeld met behulp van de wizard Azure AD Connect, wordt het volgende fout bericht weer gegeven:

![synchronisatie van wacht woord-hash is niet ingeschakeld](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect server zich in de faserings modus bevindt

Als de Azure AD Connect-server zich in de faserings modus bevindt, wordt de synchronisatie van wacht woord-hash tijdelijk uitgeschakeld en wordt de volgende fout weer gegeven:

![Azure AD Connect server zich in de faserings modus bevindt](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Geen heartbeat-gebeurtenissen voor wachtwoord-hash-synchronisatie

Elke on-premises Active Directory connector heeft een eigen wachtwoord hash-synchronisatie kanaal. Wanneer het synchronisatie kanaal voor wacht woord-hashes tot stand is gebracht en er geen wachtwoord wijzigingen worden gesynchroniseerd, wordt een heartbeat-gebeurtenis (gebeurtenis-ca 654) elke 30 minuten gegenereerd in het gebeurtenis logboek van Windows-toepassing. Voor elke on-premises Active Directory-Connector zoekt de cmdlet de overeenkomstige heartbeat-gebeurtenissen in de afgelopen drie uur. Als er geen heartbeat-gebeurtenis wordt gevonden, wordt de volgende fout geretourneerd:

![Er is geen wacht woord-hash synchronisatie hart slag gebeurtenis](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS account heeft niet de juiste machtigingen

Als het AD DS-account dat wordt gebruikt door de on-premises Active Directory-connector om wacht woord-hashes te synchroniseren, niet over de juiste machtigingen beschikt, wordt de volgende fout geretourneerd:

![Scherm afbeelding met de fout die wordt geretourneerd wanneer het AD DS-account een onjuiste gebruikers naam of wacht woord heeft.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>De gebruikers naam of het wacht woord van het AD DS account is onjuist

Als het AD DS-account dat wordt gebruikt door de on-premises Active Directory Connector voor het synchroniseren van wacht woord-hashes een onjuiste gebruikers naam of wacht woord heeft, wordt de volgende fout geretourneerd:

![Onjuiste referentie](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Het ene object synchroniseert geen wacht woorden: problemen oplossen met behulp van de taak voor probleem oplossing

U kunt de taak voor het oplossen van problemen gebruiken om te bepalen waarom een object geen wacht woorden synchroniseert.

> [!NOTE]
> De taak voor probleem oplossing is alleen beschikbaar voor Azure AD Connect versie 1.1.614.0 of hoger.

### <a name="run-the-diagnostics-cmdlet"></a>De diagnostische cmdlet uitvoeren

Problemen voor een specifiek gebruikers object oplossen:

1. Open een nieuwe Windows Power shell-sessie op uw Azure AD Connect-server met de optie **als administrator uitvoeren** .

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` of `Set-ExecutionPolicy Unrestricted` .

3. Start de wizard Azure AD Connect.

4. Ga naar de pagina **extra taken** , selecteer **problemen oplossen** en klik op **volgende**.

5. Klik op de pagina probleem oplossing op **starten** om het menu probleem oplossing in Power shell te starten.

6. Selecteer in het hoofd menu de optie **problemen met wachtwoord-hash-synchronisatie oplossen**.

7. Selecteer in het submenu de optie **wacht woord is niet gesynchroniseerd voor een specifiek gebruikers account**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Inzicht in de resultaten van de taak voor het oplossen van problemen

De taak voor het oplossen van problemen voert de volgende controles uit:

* Onderzoekt de status van het Active Directory-object in de ruimte van de Active Directory-connector, de tijdelijke tekst en de Azure AD-connector ruimte.

* Valideert dat er synchronisatie regels zijn met wachtwoord hash synchronisatie ingeschakeld en toegepast op het Active Directory-object.

* Probeert de resultaten van de laatste poging om het wacht woord voor het object te synchroniseren op te halen en weer te geven.

In het volgende diagram ziet u de resultaten van de cmdlet bij het oplossen van problemen met wachtwoord hash voor één object:

![Diagnostische uitvoer voor wachtwoord hash-synchronisatie-enkel object](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

In de rest van deze sectie worden de specifieke resultaten beschreven die worden geretourneerd door de cmdlet en de bijbehorende problemen.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Het Active Directory-object is niet geëxporteerd naar Azure AD

synchronisatie van wacht woord-hash voor dit on-premises Active Directory account mislukt omdat er geen overeenkomend object in de Azure AD-Tenant is. De volgende fout wordt geretourneerd:

![Het Azure AD-object ontbreekt](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Gebruiker heeft een tijdelijk wacht woord

Op dit moment biedt Azure AD Connect geen ondersteuning voor het synchroniseren van tijdelijke wacht woorden met Azure AD. Een wacht woord wordt als tijdelijk beschouwd als de optie **wacht woord bij wijzigen bij volgende aanmelding** is ingesteld op de on-premises Active Directory gebruiker. De volgende fout wordt geretourneerd:

![Er wordt geen tijdelijk wacht woord geëxporteerd](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>De resultaten van de laatste poging om het wacht woord te synchroniseren, zijn niet beschikbaar

Azure AD Connect slaat de resultaten van synchronisatie van wacht woord-hashes standaard gedurende zeven dagen op. Als er geen resultaten beschikbaar zijn voor het geselecteerde Active Directory-object, wordt de volgende waarschuwing weer gegeven:

![Diagnostische uitvoer voor één object-geen synchronisatie geschiedenis van het wacht woord](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Er zijn geen wacht woorden gesynchroniseerd: problemen oplossen met behulp van de diagnostische cmdlet

U kunt de `Invoke-ADSyncDiagnostics` cmdlet gebruiken om erachter te komen waarom er geen wacht woorden worden gesynchroniseerd.

> [!NOTE]
> De `Invoke-ADSyncDiagnostics` cmdlet is alleen beschikbaar voor Azure AD Connect versie 1.1.524.0 of hoger.

### <a name="run-the-diagnostics-cmdlet"></a>De diagnostische cmdlet uitvoeren

Problemen oplossen waarbij er geen wacht woorden worden gesynchroniseerd:

1. Open een nieuwe Windows Power shell-sessie op uw Azure AD Connect-server met de optie **als administrator uitvoeren** .

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` of `Set-ExecutionPolicy Unrestricted` .

3. Voer `Import-Module ADSyncDiagnostics` uit.

4. Voer `Invoke-ADSyncDiagnostics -PasswordSync` uit.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Het ene object synchroniseert geen wacht woorden: problemen oplossen met behulp van de diagnostische cmdlet

U kunt de `Invoke-ADSyncDiagnostics` cmdlet gebruiken om te bepalen waarom een object geen wacht woorden synchroniseert.

> [!NOTE]
> De `Invoke-ADSyncDiagnostics` cmdlet is alleen beschikbaar voor Azure AD Connect versie 1.1.524.0 of hoger.

### <a name="run-the-diagnostics-cmdlet"></a>De diagnostische cmdlet uitvoeren

Problemen oplossen waarbij geen wacht woorden voor een gebruiker worden gesynchroniseerd:

1. Open een nieuwe Windows Power shell-sessie op uw Azure AD Connect-server met de optie **als administrator uitvoeren** .

2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` of `Set-ExecutionPolicy Unrestricted` .

3. Voer `Import-Module ADSyncDiagnostics` uit.

4. Voer de volgende cmdlet uit:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Bijvoorbeeld:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Er zijn geen wacht woorden gesynchroniseerd: hand matige stappen voor probleem oplossing

Volg deze stappen om te bepalen waarom er geen wacht woorden worden gesynchroniseerd:

1. Is de Connect-server in de [faserings modus](how-to-connect-sync-staging-server.md)? Een server in de faserings modus synchroniseert geen wacht woorden.

2. Voer het script uit in de sectie [de status van de instellingen voor wachtwoord synchronisatie ophalen](#get-the-status-of-password-sync-settings) . Hiermee krijgt u een overzicht van de configuratie voor wachtwoord synchronisatie.  

    ![Power shell-script uitvoer van instellingen voor wachtwoord synchronisatie](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Als de functie niet is ingeschakeld in azure AD of als de synchronisatie kanaal status niet is ingeschakeld, voert u de wizard verbinding installeren uit. Selecteer **synchronisatie opties aanpassen** en selecteer wachtwoord synchronisatie opheffen. Met deze wijziging wordt de functie tijdelijk uitgeschakeld. Voer vervolgens de wizard opnieuw uit en schakel wachtwoord synchronisatie opnieuw in. Voer het script opnieuw uit om te controleren of de configuratie juist is.

4. Zoek naar fouten in het gebeurtenis logboek. Zoek naar de volgende gebeurtenissen, die duiden op een probleem:
    * Bron: "adreslijst synchronisatie" ID: 0, 611, 652, 655 als u deze gebeurtenissen ziet, hebt u een verbindings probleem. Het gebeurtenis logboek bericht bevat informatie over de forest waar u een probleem ondervindt. Zie [connectiviteits probleem](#connectivity problem)voor meer informatie.

5. Als u geen heartbeat ziet of als niets anders is gewerkt, voert u [een volledige synchronisatie van alle wacht woorden](#trigger-a-full-sync-of-all-passwords)uit. Voer het script slechts één keer uit.

6. Zie het gedeelte problemen oplossen met één object dat geen woorden synchroniseert.

### <a name="connectivity-problems"></a>Connectiviteits problemen

Hebt u verbinding met Azure AD?

Heeft het account de vereiste machtigingen voor het lezen van de wacht woord-hashes in alle domeinen? Als u verbinding maken met behulp van snelle instellingen hebt geïnstalleerd, zijn de machtigingen al correct. 

Als u aangepaste installatie hebt gebruikt, stelt u de machtigingen hand matig in door het volgende te doen:
    
1. Als u wilt zoeken naar het account dat wordt gebruikt door de Active Directory connector, start u **Synchronization Service Manager**. 
 
2. Ga naar **connectors** en zoek vervolgens naar het on-premises Active Directory forest dat u wilt oplossen. 
 
3. Selecteer de connector en klik vervolgens op **Eigenschappen**. 
 
4. Ga naar **verbinding maken met Active Directory forest**.  
    
    ![Account dat wordt gebruikt door Active Directory connector](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Noteer de gebruikers naam en het domein waarin het account zich bevindt.
    
5. Start **Active Directory gebruikers en computers** en controleer of het account dat u eerder hebt gevonden, de volgende machtigingen heeft die zijn ingesteld in de hoofdmap van alle domeinen in uw forest:
    * Directory wijzigingen repliceren
    * Wijzigingen in Directory repliceren

6. Zijn de domein controllers bereikbaar voor Azure AD Connect? Als de Connect-server geen verbinding kan maken met alle domein controllers, configureert u **alleen voorkeurs domein controller gebruiken**.  
    
    ![Domein controller die wordt gebruikt door Active Directory connector](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Ga terug naar **Synchronization Service Manager** en **Configureer de Directory partitie**. 
 
8. Selecteer uw domein in **Directory partities selecteren**, schakel het selectie vakje **alleen voorkeurs domein controllers gebruiken** in en klik vervolgens op **configureren**. 

9. Geef in de lijst de domein controllers op waarmee verbinding moet worden gebruikt voor wachtwoord synchronisatie. Dezelfde lijst wordt ook gebruikt voor importeren en exporteren. Voer deze stappen uit voor al uw domeinen.

> [!NOTE]
> Als u deze wijzigingen wilt Toep assen, start u de **Microsoft Azure AD Sync** -service (ADSync) opnieuw.

10. Als het script laat zien dat er geen heartbeat is, voert u het script uit om [een volledige synchronisatie van alle wacht woorden te activeren](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Het ene object synchroniseert geen wacht woorden: hand matige stappen voor probleem oplossing

U kunt eenvoudig problemen met wachtwoord-hash-synchronisatie oplossen door de status van een object te bekijken.

1. Zoek in **Active Directory gebruikers en computers** naar de gebruiker en controleer vervolgens of het selectie vakje **gebruiker moet wacht woord bij volgende aanmelding wijzigen** is uitgeschakeld.  

    ![Active Directory productief wacht woorden](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Als het selectie vakje is ingeschakeld, vraagt u de gebruiker om zich aan te melden en het wacht woord te wijzigen. Tijdelijke wacht woorden worden niet gesynchroniseerd met Azure AD.

2. Als het wacht woord goed is in Active Directory, volgt u de gebruiker in de synchronisatie-engine. Door de gebruiker van on-premises Active Directory naar Azure AD te volgen, kunt u zien of er een beschrijvende fout voor het object is.

    a. Start de [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Klik op **connectors**.

    c. Selecteer de **Active Directory-Connector** waar de gebruiker zich bevindt.

    d. Selecteer **ruimte Zoek connector**.

    e. Selecteer in het vak **bereik** de optie **DN of anker** en voer vervolgens de volledige DN in van de gebruiker die u wilt oplossen.

    ![Zoeken naar gebruiker in connector ruimte met DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Zoek naar de gebruiker die u zoekt en klik vervolgens op **Eigenschappen** om alle kenmerken weer te geven. Als de gebruiker zich niet in het Zoek resultaat bevindt, controleert u uw [filter regels](how-to-connect-sync-configure-filtering.md) en zorgt u ervoor dat u [Toep assen en wijzigingen controleren](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) zodat de gebruiker wordt weer gegeven in verbinding maken.

    g. Klik op **logbestand** om de details van het wachtwoord synchronisatie van het object voor de afgelopen week weer te geven.  

    ![Details van object logboek](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Als het object logboek leeg is, kan Azure AD Connect de wacht woord-hash niet lezen van Active Directory. Ga door met het oplossen van problemen met verbindings fouten. Als u een andere waarde dan **geslaagd** ziet, raadpleegt u de tabel in het [logboek voor wachtwoord synchronisatie](#password-sync-log).

    h. Selecteer het tabblad **afkomst** en controleer of ten minste één synchronisatie regel in de kolom **PasswordSync** is ingesteld op **True**. In de standaard configuratie bevindt de naam van de synchronisatie regel zich **in AD-User AccountEnabled**.  

    ![Afkomst informatie over een gebruiker](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Klik op **Eigenschappen van omgekeerd object** om een lijst met gebruikers kenmerken weer te geven.  

    ![Scherm afbeelding met de lijst met gebruikers kenmerken voor de eigenschappen van het omgekeerde object.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Controleer of er geen **cloudFiltered** -kenmerk aanwezig is. Zorg ervoor dat de domein kenmerken (domainFQDN en domainNetBios) de verwachte waarden hebben.

    j. Klik op het tabblad **connectors** . Zorg ervoor dat er Connect oren worden weer geven voor zowel on-premises Active Directory als Azure AD.

    ![Omgekeerde gegevens](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Selecteer de rij die Azure AD vertegenwoordigt, klik op **Eigenschappen** en klik vervolgens op het tabblad **afkomst** . Het object voor de connector ruimte moet een uitgaande regel in de kolom **PasswordSync** hebben ingesteld op **True**. In de standaard configuratie is de naam van de synchronisatie regel **voor Aad-gebruiker toevoegen**.  

    ![Het dialoog venster Eigenschappen van connector ruimte-object](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Wachtwoord synchronisatie logboek

De kolom Status kan de volgende waarden hebben:

| Status | Beschrijving |
| --- | --- |
| Geslaagd |Het wacht woord is gesynchroniseerd. |
| FilteredByTarget |Wacht woord is ingesteld op **gebruiker moet wacht woord bij volgende aanmelding wijzigen**. Het wacht woord is niet gesynchroniseerd. |
| NoTargetConnection |Geen object in de tekst of de ruimte van de Azure AD-connector. |
| SourceConnectorNotPresent |Er is geen object gevonden in de on-premises Active Directory-Connector ruimte. |
| TargetNotExportedToDirectory |Het object in de ruimte van de Azure AD-connector is nog niet geëxporteerd. |
| MigratedCheckDetailsForMoreInfo |De logboek vermelding is gemaakt voor het maken van 1.0.9125.0 en wordt weer gegeven in de verouderde status. |
| Fout |De service heeft een onbekende fout geretourneerd. |
| Onbekend |Er is een fout opgetreden tijdens het verwerken van een batch wacht woord-hashes.  |
| MissingAttribute |Specifieke kenmerken (bijvoorbeeld Kerberos-hash) die vereist zijn voor Azure AD Domain Services zijn niet beschikbaar. |
| RetryRequestedByTarget |Specifieke kenmerken (bijvoorbeeld Kerberos-hash) die vereist zijn voor Azure AD Domain Services, zijn niet eerder beschikbaar. Er wordt een poging gedaan om de wacht woord-hash van de gebruiker te synchroniseren. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts om problemen op te lossen

### <a name="get-the-status-of-password-sync-settings"></a>De status van instellingen voor wachtwoord synchronisatie ophalen

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Een volledige synchronisatie van alle wacht woorden activeren

> [!NOTE]
> Voer dit script slechts één keer uit. Als u dit meer dan eens moet doen, is het probleem iets anders. Neem contact op met micro soft ondersteuning om het probleem op te lossen.

U kunt een volledige synchronisatie van alle wacht woorden activeren met behulp van het volgende script:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Volgende stappen

* [Wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect synchronisatie: synchronisatie opties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
