---
title: Veelgestelde vragen over on-premises Azure AD-wachtwoord beveiliging
description: Veelgestelde vragen over Azure AD-wachtwoord beveiliging in een on-premises Active Directory Domain Services omgeving bekijken
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625124"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Veelgestelde vragen over Azure AD-wachtwoord beveiliging

In deze sectie vindt u antwoorden op veel Veelgestelde vragen over Azure AD-wachtwoord beveiliging.

## <a name="general-questions"></a>Algemene vragen

**V: welke richt lijnen moeten gebruikers worden gegeven om een veilig wacht woord te selecteren?**

De huidige richt lijnen voor dit onderwerp van micro soft zijn te vinden op de volgende koppeling:

[Richt lijnen voor micro soft-wacht woorden](https://www.microsoft.com/research/publication/password-guidance)

**V: is on-premises Azure AD-wachtwoord beveiliging ondersteund in niet-open bare Clouds?**

On-premises Azure AD-wachtwoord beveiliging wordt ondersteund in de open bare Cloud en de Arlington-Cloud. Er is geen datum aangekondigd voor Beschik baarheid in andere Clouds.

Met de Azure AD-Portal kunt u de configuratie van on-premises specifieke ' wachtwoord beveiliging voor Windows Server Active Directory ' wijzigen, zelfs bij niet-ondersteunde Clouds. dergelijke wijzigingen blijven behouden, maar worden anders nooit van kracht. Registratie van on-premises proxy agenten of-forests wordt niet ondersteund in niet-ondersteunde Clouds en dergelijke registratie pogingen zullen altijd mislukken.

**V: hoe kan ik de voor delen van Azure AD-wachtwoord beveiliging Toep assen op een subset van mijn on-premises gebruikers?**

Wordt niet ondersteund. Als Azure AD-wachtwoord beveiliging eenmaal is geïmplementeerd en ingeschakeld, krijgen alle gebruikers dezelfde beveiligings voordelen.

**V: wat is het verschil tussen een wachtwoord wijziging en een wachtwoordset (of opnieuw instellen)?**

Een wijziging van het wacht woord is wanneer een gebruiker een nieuw wacht woord kiest nadat ze kennis hebben van het oude wacht woord. Een wachtwoord wijziging is bijvoorbeeld wat er gebeurt wanneer een gebruiker zich aanmeldt bij Windows en vervolgens wordt gevraagd om een nieuw wacht woord te kiezen.

Een wachtwoordset (ook wel wacht woord opnieuw instellen genoemd) is wanneer een beheerder het wacht woord voor een account vervangt door een nieuw wacht woord, bijvoorbeeld met behulp van het beheer programma Active Directory gebruikers en computers. Voor deze bewerking is een hoog bevoegdheids niveau (meestal domein beheerder) vereist. de persoon die de bewerking uitvoert, heeft doorgaans geen kennis van het oude wacht woord. Help Desk-scenario's voeren vaak wachtwoord sets in, bijvoorbeeld bij het helpen van een gebruiker die het wacht woord is verg eten. U ziet ook wachtwoord sets wanneer er voor de eerste keer een nieuwe gebruikers account wordt gemaakt met een wacht woord.

Het wachtwoord validatie beleid gedraagt zich hetzelfde, ongeacht of er een wacht woord is gewijzigd of ingesteld. De Azure AD-Agent service voor wachtwoord beveiliging registreert verschillende gebeurtenissen om u te informeren of een wacht woord is gewijzigd of een set-bewerking is uitgevoerd.  Zie [Azure AD-controle programma voor wachtwoord beveiliging en logboek registratie](./howto-password-ban-bad-on-premises-monitor.md).

**V: Azure AD-wachtwoord beveiliging valideert bestaande wacht woorden na installatie?**

Nee: Azure AD-wachtwoord beveiliging kan alleen wachtwoord beleid afdwingen op Lees bare wacht woorden tijdens het wijzigen van een wacht woord of het instellen van een bewerking. Zodra een wacht woord is geaccepteerd door Active Directory, worden alleen verificatie-specifieke hashes van het wacht woord bewaard. Het wacht woord voor de Lees bare tekst wordt nooit bewaard, waardoor de wachtwoord beveiliging van Azure AD bestaande wacht woorden niet kan valideren.

Na de eerste implementatie van Azure AD-wachtwoord beveiliging, beginnen alle gebruikers en accounts uiteindelijk met een wacht woord voor Azure AD-wachtwoord beveiliging, waarna hun bestaande wacht woorden normaal gesp roken na verloop van tijd worden verlopen. Dit proces kan, indien gewenst, worden versneld met een eenmalig hand matige verloop tijd van wacht woorden van gebruikers accounts.

Accounts die zijn geconfigureerd met ' wacht woord nooit verloopt ' worden nooit zodanig gewijzigd dat ze hun wacht woord wijzigen, tenzij hand matige verval datum is voltooid.

**V: Waarom worden duplicaat gebeurtenissen voor het afkeuren van wacht woorden vastgelegd bij het instellen van een zwak wacht woord met behulp van de Active Directory-module gebruikers en computers beheren?**

De Active Directory-module gebruikers en computers wordt eerst geprobeerd het nieuwe wacht woord in te stellen met behulp van het Kerberos-protocol. Als de module is mislukt, wordt een tweede poging gedaan om het wacht woord in te stellen met behulp van een verouderd (SAM RPC)-protocol (de gebruikte specifieke protocollen zijn niet belang rijk). Als het nieuwe wacht woord wordt beschouwd als ongemerkt door Azure AD-wachtwoord beveiliging, worden in dit geval twee sets uitzonderings gebeurtenissen voor wachtwoord herstel vastgelegd.

**V: Waarom worden wachtwoord validatie gebeurtenissen van Azure AD-wacht woord met een lege gebruikers naam vastgelegd?**

Active Directory ondersteunt de mogelijkheid om een wacht woord te testen om te zien of de huidige wachtwoord complexiteits vereisten van het domein worden door gegeven, bijvoorbeeld met behulp van de [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) -API. Wanneer een wacht woord op deze manier wordt gevalideerd, omvat het testen ook de validatie van op wacht woord-filter-dll gebaseerde producten, zoals Azure AD-wachtwoord beveiliging, maar de gebruikers namen die zijn door gegeven aan een opgegeven wachtwoord filter-dll zijn leeg. In dit scenario zal Azure AD-wachtwoord beveiliging het wacht woord nog steeds valideren met behulp van het wachtwoord beleid dat momenteel wordt gebruikt en wordt er een gebeurtenis logboek bericht weer gegeven om het resultaat vast te leggen, maar het gebeurtenis logboek bericht bevat lege velden voor gebruikers namen.

**V: wordt it ondersteund voor het installeren van Azure AD-wachtwoord beveiliging naast elkaar met andere op wacht woorden gebaseerde producten?**

Ja. Ondersteuning voor meerdere geregistreerde wachtwoord filter-dll's is een kern functie van Windows en is niet specifiek voor Azure AD-wachtwoord beveiliging. Alle geregistreerde wachtwoord filter-dll's moeten overeenkomen voordat een wacht woord wordt geaccepteerd.

**V: hoe kan ik Azure AD-wachtwoord beveiliging in mijn Active Directory omgeving implementeren en configureren zonder Azure te gebruiken?**

Wordt niet ondersteund. Azure AD-wachtwoord beveiliging is een Azure-functie die ondersteuning biedt voor uitgebreid in een on-premises Active Directory omgeving.

**V: hoe kan ik de inhoud van het beleid op het Active Directory niveau wijzigen?**

Wordt niet ondersteund. Het beleid kan alleen worden beheerd via de Azure AD-Portal. Zie ook de vorige vraag.

**V: Waarom is DFSR vereist voor SYSVOL-replicatie?**

FRS (de voorafgaande technologie naar DFSR) heeft veel bekende problemen en wordt volledig niet ondersteund in nieuwere versies van Windows Server Active Directory. Het testen van Azure AD-wachtwoord beveiliging op nul wordt uitgevoerd op met FRS geconfigureerde domeinen.

Raadpleeg de volgende artikelen voor meer informatie:

[Het geval voor de migratie van SYSVOL-replicatie naar DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[Het einde is nigh voor FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Als het domein niet al gebruikmaakt van DFSR, moet u het migreren naar DFSR voordat u Azure AD-wachtwoord beveiliging installeert. Zie de volgende koppeling voor meer informatie:

[Migratie handleiding voor SYSVOL-replicatie: FRS naar DSF-replicatie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> De Azure AD-agent software voor wachtwoord beveiliging wordt momenteel geïnstalleerd op domein controllers in domeinen die nog steeds gebruikmaken van FRS voor SYSVOL-replicatie, maar de software zal niet goed werken in deze omgeving. Bijkomende negatieve neven effecten omvatten afzonderlijke bestanden die niet worden gerepliceerd, en SYSVOL-herstel procedures die worden uitgevoerd, maar die niet op de achtergrond worden gerepliceerd. U moet uw domein zo snel mogelijk migreren naar het gebruik van DFSR en de implementatie van Azure AD-wachtwoord beveiliging blok keren. Toekomstige versies van de software worden automatisch uitgeschakeld wanneer ze worden uitgevoerd in een domein dat nog steeds FRS gebruikt.

**V: hoeveel schijf ruimte heeft de functie nodig op de SYSVOL-share van het domein?**

De nauw keurigheid van de ruimte is afhankelijk van factoren zoals het aantal en de lengte van de verboden tokens in de lijst met algemene micro soft-gebruikers namen en de aangepaste lijst per Tenant, plus de overhead voor versleuteling. De inhoud van deze lijsten is waarschijnlijk in de toekomst groeien. Met het oog op een redelijke verwachting is dat de functie ten minste vijf (5) MB aan ruimte op de SYSVOL-share van het domein nodig heeft.

**V: Waarom moet het systeem opnieuw worden opgestart om de software van de DC-agent te installeren of bij te werken?**

Deze vereiste wordt veroorzaakt door het kern gedrag van Windows.

**V: is er een manier om een DC-agent te configureren voor het gebruik van een specifieke proxy server?**

Nee. Omdat de proxy server stateless is, is het niet belang rijk welke specifieke proxy server wordt gebruikt.

**V: is het een goed hulp middel om de Azure AD-proxy service voor wachtwoord beveiliging te implementeren naast andere services, zoals Azure AD Connect?**

Ja. De Azure AD-proxy service voor wachtwoord beveiliging en Azure AD Connect mogen nooit direct met elkaar conflicteren.

Helaas is er een incompatibiliteit gevonden tussen de versie van de Microsoft Azure AD connect agent Updater-service die is geïnstalleerd door de Azure AD-proxy software voor wachtwoord beveiliging en de versie van de service die door de [Azure Active Directory-toepassingsproxy](../manage-apps/application-proxy.md) -software is geïnstalleerd. Deze incompatibiliteit kan ertoe leiden dat de agent Updater-service geen verbinding kan maken met Azure voor software-updates. Het wordt niet aanbevolen om Azure AD-wachtwoord beveiligings proxy en Azure Active Directory-toepassingsproxy op dezelfde computer te installeren.

**V: in welke volg orde moeten de DC-agents en-proxy's worden geïnstalleerd en geregistreerd?**

Elke volg orde van de installatie van de proxy-agent, de installatie van de DC-agent, de registratie van het forest en de proxy registratie wordt ondersteund.

**V: moet ik zorgen dat de prestaties van mijn domein controllers worden gedistribueerd om deze functie te implementeren?**

De Azure AD-Agent service voor wachtwoord beveiliging heeft geen grote invloed op de prestaties van de domein controller in een bestaande gezonde Active Directory-implementatie.

Voor de meeste Active Directory implementaties is het wijzigen van wacht woorden een klein deel van de totale werk belasting op elke wille keurige domein controller. Stel dat een Active Directory domein met 10000-gebruikers accounts en een MaxPasswordAge-beleid is ingesteld op 30 dagen. Gemiddeld bevat dit domein 10000/30 = ~ 333-wachtwoord wijzigings bewerkingen elke dag, wat een klein aantal bewerkingen voor zelfs een enkele domein controller is. Houd rekening met een mogelijk slechtste scenario: Stel dat deze ~ 333-wachtwoord wijzigingen voor één enkele enkele uur zijn uitgevoerd. Dit scenario kan bijvoorbeeld optreden wanneer een groot aantal mede werkers op maandag morgen aan het werk zijn. Zelfs in dat geval kijken we eens naar ~ 333/60 minuten = zes wachtwoord wijzigingen per minuut. Dit is niet een aanzienlijke belasting.

Als uw huidige domein controllers echter al worden uitgevoerd op prestatie-beperkende niveaus (bijvoorbeeld benut uit het oogpunt van CPU, schijf ruimte, schijf-I/O, etc.), is het raadzaam extra domein controllers toe te voegen of beschik bare schijf ruimte uit te breiden voordat u deze functie implementeert. Zie ook bovenstaande vraag over het gebruik van SYSVOL-schijf ruimte hierboven.

**V: Ik wil Azure AD-wachtwoord beveiliging op slechts enkele Dc's in mijn domein testen. Is het mogelijk om wijzigingen in het gebruikers wachtwoord af te dwingen om deze specifieke Dc's te gebruiken?**

Nee. Het besturings systeem Windows-client bepaalt welke domein controller wordt gebruikt wanneer een gebruiker het wacht woord wijzigt. De domein controller wordt geselecteerd op basis van factoren zoals Active Directory site-en subnet toewijzingen, omgevings specifieke netwerk configuratie, enzovoort. Azure AD-wachtwoord beveiliging heeft geen invloed op deze factoren en kan niet bepalen welke domein controller is geselecteerd voor het wijzigen van het wacht woord van een gebruiker.

Een manier om dit doel gedeeltelijk te bereiken, is het implementeren van Azure AD-wachtwoord beveiliging op alle domein controllers in een bepaalde Active Directory-site. Deze benadering biedt een redelijke dekking voor de Windows-clients die aan die site zijn toegewezen, en daarom ook voor gebruikers die zich aanmelden bij deze clients en hun wacht woord wijzigen.

**V: als ik de DC-Agent service van Azure AD-wachtwoord beveiliging op alleen de primaire domein controller (PDC) Installeer, worden alle andere domein controllers in het domein ook beschermd?**

Nee. Wanneer het wacht woord van een gebruiker op een bepaalde niet-PDC-domein controller wordt gewijzigd, wordt het wacht woord voor lees bare tekst nooit verzonden naar de PDC (dit is een veelvoorkomende mis-bewerking). Zodra een nieuw wacht woord op een bepaalde domein controller wordt geaccepteerd, gebruikt die domein controller dat wacht woord om de verschillende verificatie-protocolspecifieke hashes van dat wacht woord te maken. vervolgens worden deze hashes in de Directory bewaard. Het wacht woord voor de Lees bare tekst wordt niet bewaard. De bijgewerkte hashes worden vervolgens gerepliceerd naar de PDC. Gebruikers wachtwoorden kunnen in sommige gevallen rechtstreeks worden gewijzigd op de primaire domein controller, afhankelijk van verschillende factoren, zoals netwerk topologie en Active Directory site ontwerp. (Zie de vorige vraag.)

In samen vatting is de implementatie van de Azure AD-Agent service voor wachtwoord beveiliging op de PDC vereist om een beveiligings dekking van 100% voor het hele domein te bereiken. Het implementeren van de functie op de primaire domein controller biedt geen Azure AD-beveiligings voordelen voor wachtwoord beveiliging voor andere domein controllers in het domein.

**V: Waarom werkt aangepaste slimme vergren deling niet, zelfs niet nadat de agents zijn geïnstalleerd in mijn on-premises Active Directory omgeving?**

Aangepaste slimme vergren deling wordt alleen ondersteund in azure AD. Wijzigingen in de aangepaste instellingen voor slim vergren delen in de Azure AD-Portal hebben geen invloed op de on-premises Active Directory omgeving, zelfs niet als de agents zijn geïnstalleerd.

**V: is er een System Center Operations Manager management pack beschikbaar voor Azure AD-wachtwoord beveiliging?**

Nee.

**V: Waarom wordt er nog steeds zwakke wacht woorden door Azure AD afgewezen, zelfs als ik het beleid in de controle modus heb geconfigureerd?**

De controle modus wordt alleen ondersteund in de on-premises Active Directory omgeving. Azure AD wordt impliciet altijd in de modus ' afdwingen ' wanneer wacht woorden worden geëvalueerd.

**V: mijn gebruikers zien het traditionele Windows-fout bericht wanneer een wacht woord wordt geweigerd door Azure AD-wachtwoord beveiliging. Is het mogelijk om dit fout bericht aan te passen zodat gebruikers weten wat er echt is gebeurd?**

Nee. Het fout bericht dat door gebruikers wordt weer gegeven wanneer een wacht woord wordt geweigerd door een domein controller, wordt beheerd door de client computer, niet door de domein controller. Dit gedrag treedt op of een wacht woord wordt geweigerd door de standaard Active Directory wachtwoord beleid of door een oplossing op basis van een wacht woord filter, zoals Azure AD-wachtwoord beveiliging.

## <a name="password-testing-procedures"></a>Test procedures voor wacht woorden

Mogelijk wilt u een aantal basis tests uitvoeren op verschillende wacht woorden om de juiste werking van de software te valideren en om een beter inzicht te krijgen in de [wachtwoord evaluatie algoritme](concept-password-ban-bad.md#how-are-passwords-evaluated). Deze sectie bevat een overzicht van een methode voor dergelijke tests die zijn ontworpen voor het produceren van Herhaal bare resultaten.

Waarom moet u deze stappen volgen? Er zijn verschillende factoren waardoor het moeilijk is om beheerde, herhaal bare testen van wacht woorden in de on-premises Active Directory omgeving uit te voeren:

* Het wachtwoord beleid is geconfigureerd en persistent gemaakt in azure, en kopieën van het beleid worden periodiek gesynchroniseerd door de on-premises DC-agent (s) met behulp van een Polling-mechanisme. De latentie inherent aan deze polling-cyclus kan leiden tot Verwar ring. Als u bijvoorbeeld het beleid configureert in azure, maar vergeet het niet te synchroniseren met de DC-agent, levert de tests mogelijk niet de verwachte resultaten op. Het polling-interval is momenteel een hardcoded keer per uur, maar wacht een uur tussen beleids wijzigingen is niet ideaal voor een interactief test scenario.
* Zodra een nieuw wachtwoord beleid is gesynchroniseerd naar een domein controller, treedt er meer latentie op wanneer het wordt gerepliceerd naar andere domein controllers. Deze vertragingen kunnen leiden tot onverwachte resultaten als u een wachtwoord wijziging test op een domein controller die de meest recente versie van het beleid nog niet heeft ontvangen.
* Door wachtwoord wijzigingen te testen via een gebruikers interface is het moeilijk om vertrouwen in uw resultaten te hebben. Het is bijvoorbeeld eenvoudig om een ongeldig wacht woord te typen in een gebruikers interface, met name omdat de meeste gebruikers interfaces van het wacht woord gebruikers invoer verbergen (bijvoorbeeld de Windows CTRL-ALT-DELETE-> gebruikers interface voor wacht woord wijzigen).
* Het is niet mogelijk om strikt te bepalen welke domein controller wordt gebruikt bij het testen van wachtwoord wijzigingen van clients die lid zijn van een domein. Het Windows-client besturingssysteem selecteert een domein controller op basis van factoren zoals Active Directory site-en subnet toewijzingen, omgevings specifieke netwerk configuratie, enzovoort.

Als u deze problemen wilt voor komen, kunt u de volgende stappen uitvoeren op basis van de opdracht regel tests voor het opnieuw instellen van wacht woorden terwijl u bent aangemeld bij een domein controller.

> [!WARNING]
> Deze procedures moeten alleen worden gebruikt in een test omgeving, omdat alle binnenkomende wachtwoord wijzigingen en opnieuw instellen worden geaccepteerd zonder validatie wanneer de DC-Agent service wordt gestopt, en om te voor komen dat de toegenomen Risico's inherent zijn aan het aanmelden bij een domein controller.

Bij de volgende stappen wordt ervan uitgegaan dat u de DC-agent hebt geïnstalleerd op ten minste één domein controller, ten minste één proxy hebt geïnstalleerd en de proxy en het forest hebben geregistreerd.

1. Meld u aan bij een domein controller met behulp van de referenties voor de domein beheerder (of andere referenties die voldoende bevoegdheden hebben om test gebruikers accounts te maken en wacht woorden opnieuw in te stellen), waarbij de software van de DC-agent is geïnstalleerd en opnieuw is opgestart.
1. Open Logboeken en navigeer naar het [gebeurtenis logboek van de DC-agent beheerder](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log).
1. Open een opdracht prompt venster met verhoogde bevoegdheden.
1. Een test account maken voor het testen van wacht woorden

   Er zijn veel manieren om een gebruikers account te maken, maar een opdracht regel optie is hier een manier om deze eenvoudig te maken tijdens herhaalde test cycli:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   Stel dat we een test account hebben gemaakt met de naam ' ContosoUser ', bijvoorbeeld:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Open een webbrowser (mogelijk moet u een afzonderlijk apparaat in plaats van uw domein controller gebruiken), Meld u aan bij de [Azure Portal](https://portal.azure.com)en blader naar Azure Active Directory > beveiligings > verificatie methoden > wachtwoord beveiliging.
1. Wijzig de Azure AD-beleids regels voor wachtwoord beveiliging als dat nodig is voor de tests die u wilt uitvoeren.  U kunt er bijvoorbeeld voor kiezen om de afgedwongen of controle modus te configureren, of u kunt besluiten om de lijst met verboden voor waarden in uw lijst met verboden wacht woorden te wijzigen.
1. Synchroniseer het nieuwe beleid door de DC-Agent service te stoppen en opnieuw te starten.

   Deze stap kan op verschillende manieren worden uitgevoerd. U kunt ook de beheer console van Service Management gebruiken door met de rechter muisknop op de Azure AD-service voor wachtwoord beveiliging DC-agent te klikken en ' opnieuw opstarten ' te kiezen. Een andere manier kan worden uitgevoerd vanuit het opdracht prompt venster, zoals in de volgende gevallen:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Controleer de Logboeken om te controleren of een nieuw beleid is gedownload.

   Telkens wanneer de DC-Agent service wordt gestopt en gestart, ziet u dat er na elkaar 2 30006 gebeurtenissen worden uitgegeven. De eerste 30006-gebeurtenis weerspiegelt het beleid dat in de cache is opgeslagen in de SYSVOL-share. De tweede gebeurtenis van 30006 (indien aanwezig) moet een bijgewerkte Tenant beleids datum hebben en zo ja, dan wordt het beleid weer gegeven dat is gedownload van Azure. De datum waarde van het Tenant beleid is momenteel gecodeerd om de tijds tempel bij benadering weer te geven dat het beleid is gedownload van Azure.
   
   Als de tweede gebeurtenis 30006 niet wordt weer gegeven, moet u het probleem oplossen voordat u doorgaat.
   
   De 30006-gebeurtenissen zien er ongeveer uit als in dit voor beeld:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Als u bijvoorbeeld tussen afgedwongen en controle modus wijzigt, wordt de vlag AuditOnly gewijzigd (het bovenstaande beleid met AuditOnly = 0 is in afgedwongen modus). wijzigingen in de lijst met aangepaste verboden wacht woorden worden niet direct weer gegeven in de bovenstaande gebeurtenis 30006 (en worden niet op een wille keurige manier geregistreerd om veiligheids redenen). Het beleid van Azure is gedownload nadat deze wijziging ook de aangepaste lijst met geblokkeerde wacht woorden bevat.

1. Voer een test uit door te proberen een nieuw wacht woord opnieuw in te stellen voor het test gebruikers account.

   Deze stap kan worden uitgevoerd in het opdracht prompt venster, bijvoorbeeld:

   ```text
   net.exe user ContosoUser <password>
   ```

   Nadat u de opdracht hebt uitgevoerd, kunt u meer informatie over het resultaat van de opdracht krijgen door te kijken in de logboeken. Resultaten van de validatie van het wacht woord worden beschreven in het onderwerp [gebeurtenis logboek van DC-agent beheerder](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) . u gebruikt dergelijke gebeurtenissen om de resultaten van uw test te valideren naast de interactieve uitvoer van de net.exe-opdrachten.

   We gaan een voor beeld proberen: er wordt geprobeerd een wacht woord in te stellen dat is verboden door de globale lijst van micro soft (deze lijst is [niet gedocumenteerd](concept-password-ban-bad.md#global-banned-password-list) , maar we kunnen hier testen met een bekende verboden periode). In dit voor beeld wordt ervan uitgegaan dat u het beleid in afgedwongen modus hebt geconfigureerd en dat er nul voor waarden zijn toegevoegd aan de lijst met geblokkeerde wacht woorden.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   Omdat bij de test een wachtwoord herstel bewerking is uitgevoerd, moet u een 10017-en een 30005-gebeurtenis voor de ContosoUser-gebruiker zien.

   De 10017-gebeurtenis moet er als volgt uitzien:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   De 30005-gebeurtenis moet er als volgt uitzien:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   Dat is leuk: we proberen nog een voor beeld. Deze keer proberen we een wacht woord in te stellen dat verboden is door de lijst met aangepaste verboden wanneer het beleid zich in de controle modus bevindt. In dit voor beeld wordt ervan uitgegaan dat u de volgende stappen hebt uitgevoerd: het beleid is geconfigureerd in de controle modus, de term ' lachrymose ' toegevoegd aan de lijst met aangepaste geblokkeerde wacht woorden en het resulterende nieuwe beleid is gesynchroniseerd met de domein controller door de DC-Agent service te activeren zoals hierboven wordt beschreven.

   Stel een variant in van het wacht woord verboden:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   Houd er rekening mee dat deze tijd is geslaagd omdat het beleid zich in de controle modus bevindt. U ziet een gebeurtenis van 10025 en een 30007 voor de ContosoUser-gebruiker.

   De 10025-gebeurtenis moet er als volgt uitzien:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   De 30007-gebeurtenis moet er als volgt uitzien:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Ga door met het testen van verschillende wacht woorden en het controleren van de resultaten in de logboeken met behulp van de procedures beschreven in de vorige stappen. Als u het beleid in de Azure Portal wilt wijzigen, vergeet dan niet om het nieuwe beleid te synchroniseren met de DC-agent zoals eerder beschreven.

We hebben procedures behandeld waarmee u het gedrag van wachtwoord validatie van Azure AD-wachtwoord beveiliging kunt testen. Het opnieuw instellen van gebruikers wachtwoorden vanaf de opdracht regel op een domein controller lijkt een oneven aantal handelingen te doen, maar zoals eerder beschreven, is het ontworpen voor het produceren van Herhaal bare resultaten. Houd bij het testen van verschillende wacht woorden rekening met de [wachtwoord evaluatie algoritme](concept-password-ban-bad.md#how-are-passwords-evaluated) , omdat deze mogelijk kan leiden tot een uitleg van de resultaten die u niet verwacht.

> [!WARNING]
> Wanneer alle tests zijn voltooid, moet u niet verg eten alle gebruikers accounts te verwijderen die zijn gemaakt voor test doeleinden.

## <a name="additional-content"></a>Aanvullende inhoud

De volgende koppelingen maken geen deel uit van de Azure AD-documentatie voor wachtwoord beveiliging, maar kunnen een nuttige bron zijn van aanvullende informatie over de functie.

[Azure AD-wachtwoord beveiliging is nu algemeen beschikbaar.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Hand leiding e-mail phishing Protection – deel 15: implementeer de Microsoft Azure AD-wachtwoord beveiligings service (voor on-premises)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD-wachtwoord beveiliging en slimme vergren delingen zijn nu beschikbaar als open bare preview.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Micro soft Premier\Unified-ondersteunings training beschikbaar

Als u meer wilt weten over het beveiligen van Azure AD-wacht woorden en het implementeren ervan in uw omgeving, kunt u profiteren van een micro soft proactieve service die beschikbaar is voor klanten met een Premier-of Unified-ondersteunings contract. De service heet Azure Active Directory: wachtwoord beveiliging. Neem contact op met uw Technical Account Manager voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u een on-premises Azure AD-vraag voor wachtwoord beveiliging hebt die hier niet wordt beantwoord, kunt u hieronder een feedback-item verzenden.

[Wachtwoordbeveiliging in Azure AD implementeren](howto-password-ban-bad-on-premises-deploy.md)