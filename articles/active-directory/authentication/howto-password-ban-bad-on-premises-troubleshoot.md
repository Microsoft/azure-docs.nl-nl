---
title: Problemen met on-premises Azure AD-wachtwoord beveiliging oplossen
description: Meer informatie over het oplossen van problemen met Azure AD-wachtwoord beveiliging voor een on-premises Active Directory Domain Services omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca00785bfe8a99b8a3d620559c4fa492ee60c63
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741742"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Problemen oplossen: on-premises Azure AD-wachtwoord beveiliging

Na de implementatie van Azure AD-wachtwoord beveiliging kan het nodig zijn om het probleem op te lossen. In dit artikel vindt u meer informatie over enkele veelvoorkomende probleemoplossings stappen.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>De DC-agent kan geen proxy vinden in de map

Het belangrijkste symptoom van dit probleem is 30017 gebeurtenissen in het gebeurtenis logboek van de DC-agent beheerder.

De gebruikelijke oorzaak van dit probleem is dat een proxy nog niet is geregistreerd. Als er een proxy is geregistreerd, kan er enige vertraging optreden vanwege een AD-replicatie latentie totdat een bepaalde DC-agent deze proxy kan zien.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>De DC-agent kan niet communiceren met een proxy

Het belangrijkste symptoom van dit probleem is 30018 gebeurtenissen in het gebeurtenis logboek van de DC-agent beheerder. Dit probleem kan verschillende mogelijke oorzaken hebben:

1. De DC-agent bevindt zich in een geïsoleerd gedeelte van het netwerk dat geen netwerk verbinding met de geregistreerde proxy (s) toestaat. Dit probleem kan onschadelijk zijn zolang andere DC-agents kunnen communiceren met de proxy (s) om wachtwoord beleid te downloaden van Azure. Zodra het beleid is gedownload, wordt het door de geïsoleerde domein controller opgehaald via de replicatie van de beleids bestanden in de SYSVOL-share.

1. De proxyclient blokkeert de toegang tot het eind punt van de RPC-eindpunttoewijzer (poort 135)

   Het installatie programma voor de Azure AD-proxy voor wachtwoord beveiliging maakt automatisch een Windows Firewall regel voor binnenkomend verkeer waarmee toegang tot poort 135 wordt toegestaan. Als deze regel later wordt verwijderd of uitgeschakeld, kunnen DC-agents niet communiceren met de proxy service. Als de ingebouwde Windows Firewall is uitgeschakeld in plaats van een ander firewall product, moet u die Firewall zodanig configureren dat toegang tot poort 135 wordt toegestaan.

1. De hostmachine blokkeert de toegang tot het RPC-eind punt (dynamisch of statisch) dat is geluisterd door de proxy service

   Het installatie programma voor de Azure AD-proxy voor wachtwoord beveiliging maakt automatisch een Windows Firewall regel voor binnenkomend verkeer waarmee toegang tot alle binnenkomende poorten die door de Azure AD-service voor wachtwoord beveiliging worden geluisterd, wordt toegestaan. Als deze regel later wordt verwijderd of uitgeschakeld, kunnen DC-agents niet communiceren met de proxy service. Als de ingebouwde Windows Firewall is uitgeschakeld in plaats van een ander firewall product, moet u die Firewall zodanig configureren dat toegang wordt toegestaan tot alle binnenkomende poorten die worden geluisterd door de Azure AD-service voor wachtwoord beveiliging. Deze configuratie kan specifieker worden gemaakt als de proxy service is geconfigureerd om te Luis teren op een specifieke statische RPC-poort (met de `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

1. De proxy-host is niet geconfigureerd om domein controllers in staat te stellen zich aan te melden bij de computer. Dit gedrag wordt bepaald via de toewijzing van de gebruikers bevoegdheid toegang tot deze computer vanaf het netwerk. Alle domein controllers in alle domeinen in het forest moeten aan deze bevoegdheid worden verleend. Deze instelling is vaak beperkt als onderdeel van een grotere inspanning voor netwerk beveiliging.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxy service kan niet communiceren met Azure

1. Zorg ervoor dat de proxy computer verbinding heeft met de eind punten die worden vermeld in de [implementatie vereisten](howto-password-ban-bad-on-premises-deploy.md).

1. Zorg ervoor dat het forest en alle proxy servers zijn geregistreerd bij dezelfde Azure-Tenant.

   U kunt deze vereiste controleren door de  `Get-AzureADPasswordProtectionProxy` -en `Get-AzureADPasswordProtectionDCAgent` Power shell-cmdlets uit te voeren en vervolgens de `AzureTenant` eigenschap van elk geretourneerd item te vergelijken. Voor een juiste werking moet de gerapporteerde Tenant naam hetzelfde zijn voor alle DC-agents en proxy servers.

   Als er een voor waarde is die niet overeenkomt met de Azure-Tenant registratie, kan dit probleem worden opgelost door de `Register-AzureADPasswordProtectionProxy` -en/of `Register-AzureADPasswordProtectionForest` Power shell-cmdlets als nodig uit te voeren, zodat u referenties van dezelfde Azure-Tenant gebruikt voor alle registraties.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC-agent kan wachtwoord beleids bestanden niet versleutelen of ontsleutelen

Azure AD-wachtwoord beveiliging heeft een kritieke afhankelijkheid van de functionaliteit voor versleuteling en ontsleuteling die is opgegeven door de micro soft Key Distribution-service. Het versleutelen of ontsleutelen van fouten kan zich manifesteren met verschillende symptomen en heeft verschillende mogelijke oorzaken.

1. Zorg ervoor dat de KDS-service is ingeschakeld en werkt op alle domein controllers met Windows Server 2012 en hoger in een domein.

   De service start modus van de KDS-service is standaard geconfigureerd als hand matig (start trigger starten). Deze configuratie betekent dat de eerste keer dat een client de service probeert te gebruiken, deze op aanvraag wordt gestart. De opstart modus van deze standaard service is acceptabel voor het werken met wachtwoord beveiliging van Azure AD.

   Als de start modus van de KDS-service is geconfigureerd voor uitgeschakeld, moet deze configuratie worden hersteld voordat Azure AD-wachtwoord beveiliging goed werkt.

   Een eenvoudige test voor dit probleem is om de KDS-service hand matig te starten via de service management MMC-console of door andere beheer hulpprogramma's te gebruiken (Voer bijvoorbeeld ' net start kdssvc ' uit vanaf een opdracht prompt console). De KDS-service wordt naar verwachting gestart en blijft actief.

   De meest voorkomende hoofd oorzaak voor de KDS-service is niet gestart, omdat het object van de Active Directory domein controller zich buiten de standaard organisatie-eenheid voor domein controllers bevindt. Deze configuratie wordt niet ondersteund door de KDS-service en wordt niet beperkt door Azure AD-wachtwoord beveiliging. De oplossing voor dit probleem is het verplaatsen van het domein controller object naar een locatie onder de standaard organisatie-eenheid voor domein controllers.

1. Incompatibele KDS-wijziging van de versleutelde buffer indeling van Windows Server 2012 R2 naar Windows Server 2016

   Er is een KDS-beveiligings oplossing geïntroduceerd in Windows Server 2016 waarmee de indeling van versleutelde KDS-buffers wordt gewijzigd. deze buffers kunnen soms niet worden ontsleuteld in Windows Server 2012 en Windows Server 2012 R2. De omgekeerde richting is het versleutelen van onKDSe buffers die zijn versleuteld in Windows Server 2012 en Windows Server 2012 R2 worden altijd gedecodeerd op Windows Server 2016 of hoger. Als op de domein controllers in uw Active Directory-domeinen een combi natie van deze besturings systemen wordt uitgevoerd, kunnen er soms problemen met de ontsleuteling van Azure AD-wachtwoord beveiliging worden gerapporteerd. Het is niet mogelijk om de timing of symptomen van deze fouten nauw keurig te voors pellen op basis van de aard van de beveiligings oplossing, en gezien dat het niet-deterministisch is welke Azure AD-wachtwoord beveiliging DC-agent op welke domein controller gegevens op een bepaald moment versleutelt.

   Er is geen oplossing voor dit probleem, anders dan voor het uitvoeren van een combi natie van deze incompatibele besturings systemen in uw Active Directory domein (en). Met andere woorden, u moet alleen Windows Server 2012-en Windows Server 2012 R2-domein controllers uitvoeren, of alleen Windows Server 2016 en hoger domein controllers uitvoeren.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>DC-agent denkt dat het forest niet is geregistreerd

Het symptoom van dit probleem is 30016 gebeurtenissen die worden vastgelegd in het DC Agent\Admin-kanaal dat deel uitmaakt van:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Er zijn twee mogelijke oorzaken voor dit probleem.

1. Het forest is inderdaad niet geregistreerd. Om het probleem op te lossen, voert u de Register-AzureADPasswordProtectionForest opdracht uit zoals beschreven in de [implementatie vereisten](howto-password-ban-bad-on-premises-deploy.md).
1. Het forest is geregistreerd, maar de DC-agent kan de registratie gegevens van het forest niet ontsleutelen. Dit geldt voor dezelfde hoofd oorzaak als het probleem #2 hierboven vermeld onder [DC-agent kan wachtwoord beleids bestanden niet versleutelen of ontsleutelen](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files). Een eenvoudige manier om deze theorie te bevestigen, is dat deze fout alleen wordt weer geven bij DC-agents die worden uitgevoerd op Windows Server 2012-of Windows Server 2012R2-domein controllers, terwijl DC-agents die worden uitgevoerd op Windows Server 2016 en latere domein controllers prima zijn. De tijdelijke oplossing is hetzelfde: werk alle domein controllers bij naar Windows Server 2016 of hoger.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Er worden zwakke wacht woorden geaccepteerd, maar dit mag niet

Dit probleem kan verschillende oorzaken hebben.

1. Uw DC-agent (s) wordt uitgevoerd op een open bare Preview-software versie die is verlopen. Zie de [Software voor de open bare preview-agent van DC is verlopen](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Uw DC-agent (s) kan geen beleid downloaden of bestaande beleids regels kunnen niet worden ontsleuteld. Controleer op mogelijke oorzaken in de bovenstaande onderwerpen.

1. De modus voor het afdwingen van het wachtwoord beleid is nog steeds ingesteld op controle. Als deze configuratie van kracht is, moet u deze opnieuw configureren om te worden afgedwongen met behulp van de Azure AD-portal voor wachtwoord beveiliging. Zie de [bewerkings modus](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)voor meer informatie.

1. Het wachtwoord beleid is uitgeschakeld. Als deze configuratie van kracht is, configureert u deze opnieuw in ingeschakeld met behulp van de Azure AD-portal voor wachtwoord beveiliging. Zie de [bewerkings modus](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)voor meer informatie.

1. U hebt de DC-agent software niet geïnstalleerd op alle domein controllers in het domein. In dit geval is het moeilijk om ervoor te zorgen dat externe Windows-clients een bepaalde domein controller doel hebben tijdens een wijziging van het wacht woord. Als u denkt dat u een bepaalde domein controller hebt waarop de DC-agent software is geïnstalleerd, kunt u controleren door het gebeurtenis logboek van de DC-agent beheerder te controleren: ongeacht het resultaat moet er ten minste één gebeurtenis zijn om het resultaat van de wachtwoord validatie te documenteren. Als er geen gebeurtenis aanwezig is voor de gebruiker waarvan het wacht woord is gewijzigd, wordt de wijziging van het wacht woord waarschijnlijk verwerkt door een andere domein controller.

   Als alternatieve test kunt u setting\changing-wacht woorden proberen terwijl u zich rechtstreeks aanmeldt bij een DC waar de DC-agent software is geïnstalleerd. Deze techniek wordt niet aanbevolen voor productie Active Directory domeinen.

   Hoewel een incrementele implementatie van de DC-agent software onder deze beperkingen wordt ondersteund, raadt micro soft ten zeerste aan de DC-agent software op alle domein controllers in een domein zo snel mogelijk te installeren.

1. De wachtwoord validatie algoritme werkt mogelijk in werkelijkheid zoals verwacht. Bekijk [hoe wacht woorden worden geëvalueerd](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe kan geen zwak wacht woord voor DSRM instellen

Active Directory valideert altijd een nieuw wacht woord voor de herstel modus van Directory Services om te controleren of het voldoet aan de vereisten voor wachtwoord complexiteit van het domein. deze validatie roept ook wachtwoord filter-dll's aan zoals Azure AD-wachtwoord beveiliging. Als het nieuwe DSRM-wacht woord wordt afgewezen, wordt het volgende fout bericht weer gegeven:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Als met Azure AD-wachtwoord beveiliging de gebeurtenis logboek gebeurtenissen voor wachtwoord validatie worden geregistreerd voor een Active Directory DSRM-wacht woord, wordt verwacht dat in de gebeurtenis logboek berichten geen gebruikers naam wordt vermeld. Dit probleem treedt op omdat het DSRM-account een lokaal account is dat geen deel uitmaakt van het werkelijke Active Directory domein.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>De promotie van de domein controller replica is mislukt vanwege een zwak wacht woord voor DSRM

Tijdens het DC-promotie proces wordt het nieuwe wacht woord voor de herstel modus van Directory Services verzonden naar een bestaande domein controller in het domein voor validatie. Als het nieuwe DSRM-wacht woord wordt afgewezen, wordt het volgende fout bericht weer gegeven:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Net zoals in het bovenstaande probleem, hebben alle Azure AD-wacht woorden voor wachtwoord validatie resultaten voor dit scenario lege gebruikers namen.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>De degradatie van de domein controller is mislukt vanwege een zwak lokaal beheerders wachtwoord

Het wordt ondersteund voor het degraderen van een domein controller waarop nog steeds de DC-agent software wordt uitgevoerd. Beheerders moeten er wel rekening mee houden dat de DC-agent software het huidige wachtwoord beleid tijdens de degradatie procedure blijft afdwingen. Het nieuwe wacht woord voor het lokale Administrator-account (opgegeven als onderdeel van de degradatie bewerking) wordt gevalideerd, net als elk ander wacht woord. Micro soft raadt aan dat beveiligde wacht woorden worden gekozen voor lokale beheerders accounts als onderdeel van een DC-degradatie procedure.

Zodra de degradatie is geslaagd en de domein controller opnieuw is opgestart en opnieuw wordt uitgevoerd als een normale lidserver, wordt de DC-agent software weer actief in een passieve modus. Deze kan op elk gewenst moment worden verwijderd.

## <a name="booting-into-directory-services-repair-mode"></a>Opstarten in de modus Active Directory-Services herstellen

Als de domein controller wordt opgestart in de modus voor het herstellen van Directory Services, detecteert de wachtwoord filter-dll van de DC-agent deze voor waarde en zorgt u ervoor dat alle wachtwoord validatie-of afdwingings activiteiten worden uitgeschakeld, ongeacht de huidige actieve beleids configuratie. De wachtwoord filter-dll van de DC-agent registreert een 10023-waarschuwings gebeurtenis in het gebeurtenis logboek van de beheerder, bijvoorbeeld:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>De software voor de open bare preview-agent is verlopen

Tijdens de open bare preview-periode van Azure AD-wachtwoord beveiliging werd de DC-agent software in code vastgelegd om de verwerking van wachtwoord validatie aanvragen voor de volgende datums te stoppen:

* Versie 1.2.65.0 stopt met het verwerken van aanvragen voor wachtwoord validatie op september 1 2019.
* Versie 1.2.25.0 en eerder gestopt de verwerking van wachtwoord validatie aanvragen op 1 2019 juli.

Naarmate de deadline nadert, worden met alle time-outversies van gelijkstroom agents in het gebeurtenis logboek van de DC-agent beheerder een 10021-gebeurtenis weer gegeven, die er als volgt uitziet:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Zodra de deadline is verstreken, worden met alle time-outversies van DC-agents in het gebeurtenis logboek van de DC-agent beheerder een 10022-gebeurtenis verzonden die er als volgt uitziet:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Aangezien de deadline alleen wordt gecontroleerd bij de eerste keer opstarten, worden deze gebeurtenissen mogelijk pas na de deadline van de kalender weer gegeven. Zodra de deadline is herkend, zullen geen negatieve effecten op de domein controller of de grotere omgeving worden uitgevoerd, anders dan alle wacht woorden, worden automatisch goedgekeurd.

> [!IMPORTANT]
> Micro soft raadt aan dat verlopen open bare preview-agents direct worden bijgewerkt naar de nieuwste versie.

Een eenvoudige manier om DC-agents in uw omgeving te detecteren die moeten worden bijgewerkt, is door de `Get-AzureADPasswordProtectionDCAgent` cmdlet uit te voeren, bijvoorbeeld:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Voor dit onderwerp is het veld SoftwareVersion duidelijk de sleutel eigenschap die u wilt bekijken. U kunt ook Power shell-filtering gebruiken om DC-agents te filteren die al of niet hoger zijn dan de vereiste basislijn versie, bijvoorbeeld:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

De Azure AD-proxy software voor wachtwoord beveiliging is niet in een wille keurige versie beperkt. Micro soft raadt u aan om de domein controller en de proxy agent te upgraden naar de meest recente versies zodra ze worden uitgebracht. De `Get-AzureADPasswordProtectionProxy` cmdlet kan worden gebruikt om proxy agenten te vinden waarvoor upgrades zijn vereist, vergelijkbaar met het bovenstaande voor beeld voor DC-agents.

Raadpleeg [de DC-agent bijwerken](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) en [de proxy service upgraden](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) voor meer informatie over specifieke upgrade procedures.

## <a name="emergency-remediation"></a>Herstel na nood geval

Als er sprake is van een situatie waarbij de DC-Agent service problemen veroorzaakt, wordt de DC-Agent service mogelijk onmiddellijk afgesloten. De dll voor wachtwoord filter van de domein controller probeert de niet-actieve service nog steeds aan te roepen en registreert waarschuwings gebeurtenissen (10012, 10013), maar alle binnenkomende wacht woorden worden tijdens die tijd geaccepteerd. De DC-Agent service kan vervolgens ook worden geconfigureerd via Windows Service Control Manager met het opstart type ' uitgeschakeld ', indien nodig.

Een andere maat regel voor herstel is het instellen van de modus inschakelen op Nee in de Azure AD-portal voor wachtwoord beveiliging. Zodra het bijgewerkte beleid is gedownload, gaat elke DC-Agent service in een quiescent-modus waarbij alle wacht woorden worden geaccepteerd als-is. Zie de [bewerkings modus](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)voor meer informatie.

## <a name="removal"></a>Procedure

Als wordt besloten de Azure AD-software voor wachtwoord beveiliging te verwijderen en alle gerelateerde status op te schonen van de domein (en) en het forest, kan deze taak worden uitgevoerd met de volgende stappen:

> [!IMPORTANT]
> Het is belang rijk dat u deze stappen in de aangegeven volg orde uitvoert. Als er een exemplaar van de proxy service wordt uitgevoerd, wordt het serviceConnectionPoint-object periodiek opnieuw gemaakt. Als er een instantie van de DC-Agent service wordt uitgevoerd, wordt het object serviceConnectionPoint en de SYSVOL-status periodiek opnieuw gemaakt.

1. Verwijder de proxy software van alle machines. Voor deze stap is het **niet** nodig om opnieuw op te starten.
2. Verwijder de software van de DC-agent van alle domein controllers. Voor deze stap **moet** de computer opnieuw worden opgestart.
3. Verwijder hand matig alle proxy service verbindings punten in elke domein naamgevings context. De locatie van deze objecten kan worden gedetecteerd met de volgende Active Directory Power shell-opdracht:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Laat het sterretje (*) aan het einde van de waarde van de $keywords variabele niet weg.

   De resulterende object (en) die via de `Get-ADObject` opdracht zijn gevonden, kunnen vervolgens worden gesleept naar `Remove-ADObject` of hand matig worden verwijderd.

4. Verwijder hand matig alle verbindings punten van de DC-agent in elke domein naamgevings context. Er kan één van deze objecten per domein controller in het forest zijn, afhankelijk van hoe ver de software is geïmplementeerd. De locatie van het object kan worden gedetecteerd met de volgende Active Directory Power shell-opdracht:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   De resulterende object (en) die via de `Get-ADObject` opdracht zijn gevonden, kunnen vervolgens worden gesleept naar `Remove-ADObject` of hand matig worden verwijderd.

   Laat het sterretje (*) aan het einde van de waarde van de $keywords variabele niet weg.

5. De configuratie status op forestniveau hand matig verwijderen. De configuratie status van de forest wordt onderhouden in een container in de configuratie naamgevings context van Active Directory. Dit kan als volgt worden gedetecteerd en verwijderd:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Verwijder hand matig alle aan SYSVOL gerelateerde status door de volgende map en de inhoud ervan hand matig te verwijderen:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Zo nodig kan dit pad ook lokaal worden geopend op een bepaalde domein controller. de standaard locatie zou er ongeveer als volgt uitzien:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Dit pad is anders als de SYSVOL-share is geconfigureerd op een niet-standaard locatie.

## <a name="next-steps"></a>Volgende stappen

[Veelgestelde vragen over Azure AD-wachtwoord beveiliging](howto-password-ban-bad-on-premises-faq.md)

Voor meer informatie over de algemene en aangepaste lijst met verboden wacht woorden raadpleegt u het artikel [been](concept-password-ban-bad.md)
