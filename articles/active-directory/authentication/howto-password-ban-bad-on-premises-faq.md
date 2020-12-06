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
ms.openlocfilehash: 6d5517afe7407da7428d4a83f3d2de67836280c7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741895"
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

Niet ondersteund. Als Azure AD-wachtwoord beveiliging eenmaal is geïmplementeerd en ingeschakeld, krijgen alle gebruikers dezelfde beveiligings voordelen.

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

Niet ondersteund. Azure AD-wachtwoord beveiliging is een Azure-functie die ondersteuning biedt voor uitgebreid in een on-premises Active Directory omgeving.

**V: hoe kan ik de inhoud van het beleid op het Active Directory niveau wijzigen?**

Niet ondersteund. Het beleid kan alleen worden beheerd via de Azure AD-Portal. Zie ook de vorige vraag.

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