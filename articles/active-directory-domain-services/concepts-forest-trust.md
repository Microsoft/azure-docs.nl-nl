---
title: Hoe vertrouwens relaties werken voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over hoe vertrouwens relaties tussen forests werken met Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 5c72ab7d085de558ee95f3c602ccc6be6160b322
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620202"
---
# <a name="how-trust-relationships-work-for-resource-forests-in-azure-active-directory-domain-services"></a>Hoe vertrouwens relaties werken voor bron-forests in Azure Active Directory Domain Services

Active Directory Domain Services (AD DS) biedt beveiliging voor meerdere domeinen of forests via vertrouwens relaties tussen domeinen en forests. Voordat verificatie kan plaatsvinden tussen vertrouwens relaties, moet Windows eerst controleren of het domein dat wordt aangevraagd door een gebruiker, computer of service, een vertrouwens relatie heeft met het domein van het aangevraagde account.

Als u wilt controleren op deze vertrouwens relatie, berekent het Windows-beveiligings systeem een vertrouwenspad tussen de domein controller (DC) voor de server die de aanvraag ontvangt en een domein controller in het domein van het aangevraagde account.

De toegangs beheer mechanismen van AD DS en het Windows-gedistribueerde beveiligings model bieden een omgeving voor de werking van domein-en forest-vertrouwens relaties. Voor een goede werking van deze vertrouwens relaties moet elke bron of computer een direct vertrouwenspad hebben naar een domein controller in het domein waarin deze zich bevindt.

Het vertrouwenspad wordt geïmplementeerd door de Net Logon-service met een geverifieerde RPC-verbinding (Remote Procedure Call) naar de vertrouwde domein instantie. Een beveiligd kanaal kan ook worden uitgebreid naar andere AD DS domeinen door middel van vertrouwens relaties tussen domeinen. Dit beveiligde kanaal wordt gebruikt om beveiligings informatie, inclusief beveiligings-id's (Sid's) voor gebruikers en groepen, op te halen en te verifiëren.

Zie [concepten en functies][create-forest-trust]van het resource-forest voor een overzicht van de manier waarop vertrouwens relaties van toepassing zijn op Azure AD DS.

Om aan de slag te gaan met het gebruik van vertrouwens relaties in azure AD DS, moet u [een beheerd domein maken dat forest-vertrouwens relaties gebruikt][tutorial-create-advanced].

## <a name="trust-relationship-flows"></a>Stromen van vertrouwens relatie

De stroom van beveiligde communicatie via vertrouwens relaties bepaalt de elasticiteit van een vertrouwens relatie. Hoe u een vertrouwens relatie maakt of configureert, bepaalt hoe ver de communicatie binnen of tussen forests uitvalt.

De communicatie stroom over vertrouwens relaties wordt bepaald door de richting van de vertrouwens relatie. Vertrouwens relaties kunnen een eenrichtings-of twee richting zijn, en kunnen transitief of niet-transitief zijn.

In het volgende diagram ziet u dat alle domeinen in *structuur 1* en *structuur 2* standaard transitieve vertrouwens relaties hebben. Als gevolg hiervan kunnen gebruikers in *structuur 1* toegang krijgen tot bronnen in domeinen in *structuur 2* en kunnen gebruikers in *structuur 1* toegang krijgen tot resources in *structuur 2* wanneer de juiste machtigingen worden toegewezen aan de resource.

![Diagram van vertrouwens relaties tussen twee forests](./media/concepts-forest-trust/trust-relationships.png)

### <a name="one-way-and-two-way-trusts"></a>Eenrichtings-en twee richtings vertrouwensrelaties

Vertrouwens relaties maken het mogelijk om toegang tot resources in te stellen op één of twee richtingen.

Een eenzijdige vertrouwens relatie is een eenrichtings verificatie-pad tussen twee domeinen. In een eenrichtings vertrouwensrelatie tussen *domein a* en *domein B* hebben gebruikers in *domein a* toegang tot bronnen in *domein B*. Gebruikers in *domein B* hebben echter geen toegang tot bronnen in *domein A*.

Sommige eenrichtings vertrouwensrelaties kunnen niet-transitief of transitief zijn, afhankelijk van het type vertrouwen dat wordt gemaakt.

In een twee richtings vertrouwensrelatie vertrouwen domein *a* domein *B* en *domein B* *domein a*. Deze configuratie betekent dat verificatie aanvragen kunnen worden door gegeven tussen de twee domeinen in beide richtingen. Sommige twee richtings relaties kunnen niet-transitief of transitief zijn, afhankelijk van het type vertrouwen dat wordt gemaakt.

Alle domein vertrouwensrelaties in een AD DS forest zijn twee richtings-transitieve vertrouwens relaties. Wanneer een nieuw onderliggend domein wordt gemaakt, wordt automatisch een transitieve twee richtings vertrouwensrelatie gemaakt tussen het nieuwe onderliggende domein en het bovenliggende domein.

### <a name="transitive-and-non-transitive-trusts"></a>Transitieve en niet-transitieve vertrouwens relaties

Transitiviteit bepaalt of een vertrouwens relatie kan worden uitgebreid buiten de twee domeinen waarmee deze is gevormd.

* Een transitieve vertrouwens relatie kan worden gebruikt om vertrouwens relaties met andere domeinen uit te breiden.
* Een niet-transitieve vertrouwens relatie kan worden gebruikt om vertrouwens relaties met andere domeinen te weigeren.

Telkens wanneer u een nieuw domein in een forest maakt, wordt automatisch een transitieve twee richtings relatie tussen het nieuwe domein en het bovenliggende domein gemaakt. Als onderliggende domeinen worden toegevoegd aan het nieuwe domein, loopt het vertrouwenspad omhoog door de domein hiërarchie die het eerste vertrouwenspad uitbreidt dat is gemaakt tussen het nieuwe domein en het bovenliggende domein. Transitieve vertrouwens relaties stromen omhoog via een domein structuur, terwijl deze worden gevormd, waardoor transitieve vertrouwens relaties tussen alle domeinen in de domein structuur worden gemaakt.

Verificatie aanvragen volgen deze vertrouwens paden, dus accounts van elk domein in het forest kunnen worden geverifieerd door elk ander domein in het forest. Met één aanmeldings proces kunnen accounts met de juiste machtigingen toegang krijgen tot bronnen in elk domein in het forest.

## <a name="forest-trusts"></a>Forestvertrouwensrelaties

Met forest-vertrouwens relaties kunt u een gesegmenteerde AD DS-infra structuur beheren en de toegang tot resources en andere objecten in meerdere forests ondersteunen. Forest-vertrouwens relaties zijn handig voor service providers, bedrijven die fusies of verwervingen, samen werkende bedrijfs extranets en bedrijven die een oplossing zoeken voor autonomie op het niveau van beheer.

Met forest-vertrouwens relaties kunt u twee verschillende forests koppelen aan een eenrichtings-of twee richtings vertrouwensrelatie. Met een forestvertrouwensrelatie kunnen beheerders twee AD DS forests verbinden met één vertrouwens relatie om een naadloze verificatie-en autorisatie-ervaring te bieden in de forests.

Een forestvertrouwensrelatie kan alleen worden gemaakt tussen een forest-hoofd domein in een forest en een forest-hoofd domein in een ander forest. Forestvertrouwensrelaties kunnen alleen worden gemaakt tussen twee forests en kunnen niet impliciet worden uitgebreid naar een derde forest. Dit gedrag houdt in dat als een forestvertrouwensrelatie tussen *forest 1* en *forest 2* wordt gemaakt en een andere forest-vertrouwens relatie wordt gemaakt tussen *forest 2* en *forest 3*, *forest 1* geen impliciete vertrouwens relatie heeft met *forest 3*.

In het volgende diagram ziet u twee afzonderlijke forest-vertrouwens relaties tussen drie AD DS forests in één organisatie.

![Diagram van forest-vertrouwens relaties binnen één organisatie](./media/concepts-forest-trust/forest-trusts-diagram.png)

Deze voorbeeld configuratie biedt de volgende toegang:

* Gebruikers in *forest 2* hebben toegang tot bronnen in elk domein in *forest 1* of *forest 3*
* Gebruikers in *forest 3* hebben toegang tot bronnen in elk domein in *forest 2*
* Gebruikers in *forest 1* hebben toegang tot bronnen in elk domein in *forest 2*

Met deze configuratie kunnen gebruikers in *forest 1* geen toegang krijgen tot resources in *forest 3* of andersom. Als u wilt dat gebruikers in *forest 1* en *forest 3* bronnen kunnen delen, moet een transitieve twee richtings vertrouwensrelatie tussen de twee forests worden gemaakt.

Als een eenrichtings vertrouwensrelatie tussen twee forests tot stand is gebracht, kunnen leden van het vertrouwde forest bronnen gebruiken die zich in het vertrouwende forest bevinden. De vertrouwens relatie werkt echter in slechts één richting.

Als er bijvoorbeeld een eenrichtings vertrouwensrelatie tussen *forest 1* (het vertrouwde forest) en *forest 2* (het vertrouwende forest) is gemaakt:

* Leden van *forest 1* hebben toegang tot bronnen die zich in *forest 2* bevinden.
* Leden van *forest 2* hebben geen toegang tot bronnen die zich in *forest 1* bevinden met dezelfde vertrouwens relatie.

> [!IMPORTANT]
> Azure AD Domain Services resource-forest ondersteunt alleen een eenrichtings vertrouwensrelatie voor een forest naar on-premises Active Directory.

### <a name="forest-trust-requirements"></a>Vereisten voor forestvertrouwensrelatie

Voordat u een forestvertrouwensrelatie kunt maken, moet u controleren of de juiste Domain Name System-infra structuur (DNS) aanwezig is. Forestvertrouwensrelaties kunnen alleen worden gemaakt wanneer een van de volgende DNS-configuraties beschikbaar is:

* Een enkele DNS-basis server is de DNS-basis server voor beide forest-DNS-naam ruimten: de hoofd zone bevat delegaties voor elk van de DNS-naam ruimten en de basis servers van alle DNS-servers bevatten de DNS-basis server.
* Wanneer er geen gedeelde basis-DNS-server is en de DNS-basis servers in elke forest-naam ruimte gebruiken, worden voor elke DNS-naam ruimte query's voor de namen in de andere naam ruimte gerouteerd.

    > [!IMPORTANT]
    > Azure AD Domain Services resource-forest moet deze DNS-configuratie gebruiken. Het hosten van een andere DNS-naam ruimte dan het resource forest DNS namespace is geen functie van Azure AD Domain Services. Voorwaardelijke doorstuur servers is de juiste configuratie.

* Wanneer er geen gedeelde basis-DNS-server is en de DNS-basis servers in elke forest-naam ruimte worden gebruikt, worden de secundaire DNS-zones in elke DNS-naam ruimte geconfigureerd om query's voor namen in de andere naam ruimte te routeren.

Als u een forestvertrouwensrelatie wilt maken, moet u lid zijn van de groep domein Administrators (in het forest-hoofd domein) of de groep Ondernemings Administrators in Active Directory. Aan elke vertrouwens relatie wordt een wacht woord toegewezen dat de beheerders in beide forests moeten kennen. Leden van de ondernemings Administrators in beide forests kunnen de vertrouwens relaties in beide forests tegelijk maken. in dit scenario wordt een wacht woord dat cryptografisch wille keurig is, automatisch gegenereerd en geschreven voor beide forests.

De uitgaande forest-vertrouwens relatie voor Azure AD Domain Services wordt gemaakt in de Azure Portal. U maakt de vertrouwens relatie niet hand matig met het beheerde domein zelf. De vertrouwens relatie van het binnenkomende forest moet worden geconfigureerd door een gebruiker met de bevoegdheden die eerder in het on-premises Active Directory zijn vermeld.

## <a name="trust-processes-and-interactions"></a>Vertrouwens processen en interacties

Veel trans acties tussen domeinen en interforests zijn afhankelijk van domein-of forest-vertrouwens relaties om verschillende taken uit te voeren. In deze sectie worden de processen en interacties beschreven die zich voordoen als bronnen worden gebruikt voor vertrouwens relaties en verificatie verwijzingen worden geëvalueerd.

### <a name="overview-of-authentication-referral-processing"></a>Overzicht van verwerking van verificatie referenties

Wanneer een verificatie aanvraag wordt verwezen naar een domein, moet de domein controller in dat domein bepalen of er een vertrouwens relatie bestaat met het domein waarvan de aanvraag afkomstig is. De richting van de vertrouwens relatie en of de vertrouwens relatie transitief of niet-transitief is, moet ook worden vastgesteld voordat de gebruiker de toegang tot bronnen in het domein verifieert. Welk verificatie proces plaatsvindt tussen vertrouwde domeinen, is afhankelijk van het verificatie protocol dat in gebruik is. De Kerberos V5-en NTLM-protocollen verwerken verwijzingen voor verificatie naar een domein op een andere manier

### <a name="kerberos-v5-referral-processing"></a>Verwerking van Kerberos V5-verwijzingen

Het Kerberos V5-verificatie protocol is afhankelijk van de Net Logon-service op domein controllers voor client verificatie en autorisatie-informatie. Het Kerberos-protocol maakt verbinding met een online Key Distribution Center (KDC) en het Active Directory-account Archief voor sessie tickets.

Het Kerberos-protocol maakt ook gebruik van vertrouwens relaties voor kruislings realm ticket-granting Services (TGS) en voor het valideren van PACs (privilege attribute Certificates) via een beveiligd kanaal. Het Kerberos-protocol voert cross-realm-verificatie alleen uit met niet-Windows-besturings systeem Kerberos-realms zoals een MIT Kerberos-realm en hoeft niet te communiceren met de Net Logon-service.

Als de client Kerberos V5 gebruikt voor verificatie, vraagt het een ticket aan bij de server in het doel domein van een domein controller in het account domein. Het Kerberos KDC fungeert als een vertrouwde intermediair tussen de client en de server en biedt een sessie sleutel waarmee de twee partijen elkaar kunnen verifiëren. Als het doel domein verschilt van het huidige domein, volgt de KDC een logisch proces om te bepalen of een verificatie aanvraag kan worden genoemd:

1. Wordt het huidige domein rechtstreeks vertrouwd door het domein van de server die wordt aangevraagd?
    * Zo ja, verzendt u de client een verwijzing naar het aangevraagde domein.
    * Als dat niet het geval is, gaat u naar de volgende stap.

2. Bestaat er een transitieve vertrouwens relatie tussen het huidige domein en het volgende domein op het vertrouwenspad?
    * Zo ja, stuur de client een verwijzing naar het volgende domein op het vertrouwenspad.
    * Als dat niet het geval is, verzendt u de client een bericht dat het aanmelden is geweigerd.

### <a name="ntlm-referral-processing"></a>NTLM-verwijzings verwerking

Het NTLM-verificatie protocol is afhankelijk van de Net Logon-service op domein controllers voor client verificatie-en autorisatie-informatie. Met dit protocol worden clients geverifieerd die geen gebruikmaken van Kerberos-verificatie. NTLM maakt gebruik van vertrouwens relaties voor het door geven van verificatie aanvragen tussen domeinen.

Als de client NTLM gebruikt voor verificatie, gaat de eerste verificatie aanvraag rechtstreeks van de client naar de bron server in het doel domein. Deze server maakt een uitdaging waarmee de client reageert. De server verzendt vervolgens de reactie van de gebruiker naar een domein controller in het domein van het computer account. Deze domein controller controleert het gebruikers account op basis van de data base van de beveiligings accounts.

Als het account niet in de data base bestaat, bepaalt de domein controller of de Pass-Through-verificatie moet worden uitgevoerd, de aanvraag wordt doorgestuurd of de aanvraag wordt geweigerd met behulp van de volgende logica:

1. Heeft het huidige domein een directe vertrouwens relatie met het domein van de gebruiker?
    * Zo ja, de domein controller verzendt de referenties van de client naar een domein controller in het domein van de gebruiker voor Pass-Through-verificatie.
    * Als dat niet het geval is, gaat u naar de volgende stap.

2. Heeft het huidige domein een transitieve vertrouwens relatie met het domein van de gebruiker?
    * Zo ja, geeft u de verificatie aanvraag door aan het volgende domein in het vertrouwenspad. Deze domein controller herhaalt het proces door de referenties van de gebruiker te controleren op basis van een eigen data base met beveiligings accounts.
    * Als dat niet het geval is, verzendt u de client een bericht dat de aanmelding is geweigerd.

### <a name="kerberos-based-processing-of-authentication-requests-over-forest-trusts"></a>Op Kerberos gebaseerde verwerking van verificatie aanvragen via forest-vertrouwens relaties

Wanneer twee forests zijn verbonden door een forestvertrouwensrelatie, kunnen verificatie aanvragen die worden gedaan via de protocollen Kerberos V5 of NTLM, worden gerouteerd tussen forests om toegang te bieden tot bronnen in beide forests.

Wanneer een forest-vertrouwens relatie voor het eerst tot stand is gebracht, verzamelt elk forest alle vertrouwde naam ruimten in het partner-forest en slaat de gegevens op in een [vertrouwd domein object](#trusted-domain-object). Voor beelden van vertrouwde naam ruimten zijn domein namen, achtervoegsels voor user principal name (UPN), achtervoegsels voor Service Principal Name (SPN) en beveiligings-id's (SID'S) die worden gebruikt in het andere forest. TDO-objecten worden gerepliceerd naar de globale catalogus.

Voordat verificatie protocollen het pad naar de forestvertrouwensrelatie kunnen volgen, moet de Service Principal Name (SPN) van de bron computer worden omgezet naar een locatie in het andere forest. Een SPN kan een van de volgende namen hebben:

* De DNS-naam van een host.
* De DNS-naam van een domein.
* De DN-naam van een object voor het service verbindings punt.

Wanneer een werk station in het ene forest probeert toegang te krijgen tot gegevens op een bron computer in een ander forest, neemt het Kerberos-verificatie proces contact op met de domein controller voor een service ticket naar de SPN van de bron computer. Zodra de domein controller de globale catalogus heeft opgevraagd en wordt vastgesteld dat de SPN zich niet in hetzelfde forest bevindt als de domein controller, stuurt de domein controller een verwijzing naar het bovenliggende domein terug naar het werk station. Op dat moment vraagt het werk station het bovenliggende domein voor het service ticket en blijft de referentie keten volgen totdat het het domein bereikt waarin de bron zich bevindt.

Het volgende diagram en de stappen bieden een gedetailleerde beschrijving van het Kerberos-verificatie proces dat wordt gebruikt wanneer computers met Windows proberen toegang te krijgen tot bronnen van een computer die zich in een ander forest bevindt.

![Diagram van het Kerberos-proces via een forest-vertrouwens relatie](media/concepts-forest-trust/kerberos-over-forest-trust-process-diagram.png)

1. *Gebruiker1* meldt zich aan bij *Werkstation1* met behulp van referenties van het domein *Europe.tailspintoys.com* . De gebruiker probeert vervolgens toegang te krijgen tot een gedeelde bron op *FileServer1* die zich in het forest *USA.wingtiptoys.com* bevindt.

2. *Werkstation1* neemt contact op met het Kerberos KDC op een domein controller in het domein *ChildDC1* en vraagt een service ticket aan voor de SPN van *FileServer1* .

3. *ChildDC1* vindt de SPN niet in de domein database en voert een query uit op de globale catalogus om te zien of er domeinen in het *tailspintoys.com* -forest deze SPN bevatten. Omdat een globale catalogus is beperkt tot een eigen forest, kan de SPN niet worden gevonden.

    De globale catalogus controleert vervolgens de bijbehorende data base op informatie over de forest-vertrouwens relaties die zijn ingesteld met het forest. Indien gevonden, worden de naam achtervoegsels die worden vermeld in het forest Trusted Domain object (TDO), vergeleken met het achtervoegsel van de SPN van het doel om een overeenkomst te vinden. Wanneer een overeenkomst is gevonden, biedt de globale catalogus een routerings Hint terug naar *ChildDC1*.

    Routerings hints helpen verificatie aanvragen naar het doelforest te sturen. Hints worden alleen gebruikt als alle traditionele verificatie kanalen, zoals lokale domein controller en vervolgens globale catalogus, geen SPN-naam hebben gevonden.

4. *ChildDC1* verzendt een verwijzing naar het bovenliggende domein terug naar *Werkstation1*.

5. *Werkstation1* neemt contact op met een domein controller in *ForestRootDC1* (het bovenliggende domein) voor een verwijzing naar een domein controller (*ForestRootDC2*) in het forest-hoofd domein van het *wingtiptoys.com* -forest.

6. *Werkstation1* neemt contact op met *ForestRootDC2* in het *wingtiptoys.com* -forest voor een service ticket voor de aangevraagde service.

7. *ForestRootDC2* neemt contact op met de globale catalogus om de SPN te vinden en de globale catalogus vindt een overeenkomst voor de SPN en stuurt deze terug naar *ForestRootDC2*.

8. *ForestRootDC2* stuurt de verwijzing naar *USA.wingtiptoys.com* terug naar *Werkstation1*.

9. *Werkstation1* neemt contact op met het KDC op *ChildDC2* en onderhandelt het ticket voor *gebruiker1* om toegang te krijgen tot *FileServer1*.

10. Zodra *Werkstation1* een service ticket heeft, stuurt het het service ticket naar *FileServer1*, dat de beveiligings referenties van *gebruiker1* leest en een toegangs token dienovereenkomstig bouwt.

## <a name="trusted-domain-object"></a>Vertrouwd domein object

Elke vertrouwens relatie tussen domeinen of forests binnen een organisatie wordt vertegenwoordigd door een vertrouwd domein object (TDO) dat is opgeslagen in de *systeem* container binnen het domein.

### <a name="tdo-contents"></a>TDO-inhoud

De informatie in een TDO is afhankelijk van het feit of een TDO is gemaakt door een vertrouwens relatie van een domein of door een forestvertrouwensrelatie.

Wanneer een domein vertrouwensrelatie wordt gemaakt, worden kenmerken zoals de DNS-domein naam, de domein-SID, het vertrouwens type, de transitiviteit van de vertrouwens relatie en de wederzijdse domein naam weer gegeven in het TDO. Forest-vertrouwens relatie TDOs slaan extra kenmerken op om alle vertrouwde naam ruimten van het partner-forest te identificeren. Deze kenmerken omvatten namen van domein structuren, achtervoegsels van de user principal name (UPN), Service Principal Name (SPN) achtervoegsels en beveiligings-ID (SID)-naam ruimten.

Omdat vertrouwens relaties worden opgeslagen in Active Directory als TDOs, hebben alle domeinen in een forest kennis van de vertrouwens relaties die in het hele forest aanwezig zijn. Wanneer twee of meer forests zijn samengevoegd via forestvertrouwensrelaties, hebben de forest-hoofd domeinen in elk forest kennis van de vertrouwens relaties die aanwezig zijn in alle domeinen in vertrouwde forests.

### <a name="tdo-password-changes"></a>Wachtwoord wijzigingen voor TDO

Beide domeinen in een vertrouwens relatie delen een wacht woord dat is opgeslagen in het TDO-object in Active Directory. Als onderdeel van het account onderhoud wordt elke 30 dagen dat de vertrouwende domein controller het wacht woord dat is opgeslagen in het TDO, gewijzigd. Omdat alle twee richtings vertrouwensrelaties in feite een 2 1-richtings vertrouwensrelatie zijn, vindt het proces twee keer plaats voor twee richtings vertrouwensrelaties.

Een vertrouwens relatie heeft een vertrouwens relatie en een vertrouwde zijde. Op de vertrouwde zijde kan een Beschrijf bare domein controller worden gebruikt voor het proces. Op de vertrouwende zijde voert de PDC-emulator de wachtwoord wijziging uit.

Als u een wacht woord wilt wijzigen, voert u de volgende stappen uit op de domein controllers:

1. Met de PDC-emulator (primaire domein controller) in het vertrouwende domein maakt u een nieuw wacht woord. Een domein controller in het vertrouwde domein initieert nooit de wachtwoord wijziging. Het wordt altijd gestart door de PDC-emulator van het vertrouwende domein.

2. Met de PDC-emulator in het vertrouwende domein wordt het *OldPassword* -veld van het TDO-object ingesteld op het huidige *NieuwWachtwoord* -veld.

3. De PDC-emulator in het vertrouwende domein stelt het veld *NieuwWachtwoord* van het TDO-object in op het nieuwe wacht woord. Als u een kopie van het vorige wacht woord houdt, is het mogelijk om terug te keren naar het oude wacht woord als de domein controller in het vertrouwde domein de wijziging niet kan ontvangen, of als de wijziging niet wordt gerepliceerd voordat een aanvraag wordt gemaakt waarbij het nieuwe wacht woord wordt gebruikt.

4. De PDC-emulator in het vertrouwende domein brengt een externe aanroep naar een domein controller in het vertrouwde domein om het wacht woord voor het vertrouwens account in te stellen op het nieuwe wacht woord.

5. De domein controller in het vertrouwde domein wijzigt het wacht woord van de vertrouwens relatie met het nieuwe wacht woord.

6. Aan beide zijden van de vertrouwens relatie worden de updates gerepliceerd naar de andere domein controllers in het domein. In het vertrouwende domein activeert de wijziging een urgente replicatie van het vertrouwde domein object.

Het wacht woord is nu gewijzigd op beide domein controllers. Bij normale replicatie worden de TDO-objecten gedistribueerd naar de andere domein controllers in het domein. Het is echter mogelijk dat de domein controller in het vertrouwende domein het wacht woord wijzigt zonder dat een domein controller in het vertrouwde domein is bijgewerkt. Dit scenario kan zich voordoen omdat een beveiligd kanaal, dat vereist is voor het verwerken van de wachtwoord wijziging, niet tot stand kan worden gebracht. Het is ook mogelijk dat de domein controller in het vertrouwde domein op een bepaald moment tijdens het proces niet beschikbaar is en het bijgewerkte wacht woord mogelijk niet kan ontvangen.

De domein controller in het vertrouwende domein wijzigt nooit het nieuwe wacht woord, tenzij het is geverifieerd (een beveiligd kanaal instellen) met behulp van het nieuwe wacht woord om te kunnen omgaan met situaties waarin de wachtwoord wijziging niet wordt doorgevoerd. Dit gedrag is waarom zowel de oude als de nieuwe wacht woorden worden bewaard in het TDO-object van het vertrouwende domein.

Een wachtwoord wijziging is pas voltooid als het wacht woord is geslaagd. Het oude, opgeslagen wacht woord kan worden gebruikt via het beveiligde kanaal totdat de domein controller in het vertrouwde domein het nieuwe wacht woord ontvangt, waardoor het mogelijk is om een ononderbroken service in te scha kelen.

Als verificatie met het nieuwe wacht woord mislukt omdat het wacht woord ongeldig is, probeert de vertrouwens relatie van de domein controller te verifiëren met het oude wacht woord. Als de verificatie met het oude wacht woord is gelukt, wordt het wachtwoord wijzigings proces binnen 15 minuten hervat.

Updates voor een wacht woord moeten binnen 30 dagen worden gerepliceerd naar de domein controllers van beide zijden van de vertrouwens relatie. Als het wacht woord van de vertrouwens relatie na 30 dagen wordt gewijzigd en een domein controller alleen het N-2-wacht woord heeft, kan de vertrouwens relatie van de vertrouwens zijde niet worden gebruikt en kan er geen beveiligd kanaal op de vertrouwde zijde worden gemaakt.

## <a name="network-ports-used-by-trusts"></a>Netwerk poorten die worden gebruikt door vertrouwens relaties

Omdat vertrouwens relaties tussen verschillende netwerk grenzen moeten worden geïmplementeerd, moeten ze mogelijk een of meer firewalls omvatten. Als dit het geval is, kunt u tunnel vertrouwen verkeer via een firewall of specifieke poorten in de firewall openen zodat het verkeer kan passeren.

> [!IMPORTANT]
> Active Directory Domain Services biedt geen ondersteuning voor het beperken van Active Directory RPC-verkeer naar specifieke poorten.

Lees de sectie **Windows Server 2008 en latere versies** van het Microsoft ondersteuning artikel [een firewall configureren voor Active Directory domeinen en vertrouwens relaties](https://support.microsoft.com/help/179442/how-to-configure-a-firewall-for-domains-and-trusts) voor meer informatie over de poorten die nodig zijn voor een forestvertrouwensrelatie.

## <a name="supporting-services-and-tools"></a>Ondersteunende services en hulpprogram ma's

Voor het ondersteunen van vertrouwens relaties en verificatie, worden er een aantal extra functies en beheer Programma's gebruikt.

### <a name="net-logon"></a>Net Logon

De Net Logon-service onderhoudt een beveiligd kanaal van een Windows-computer naar een domein controller. Het wordt ook gebruikt in de volgende processen die betrekking hebben op vertrouwen:

* Het instellen en beheren van vertrouwens relaties: Net Logon helpt bij het onderhouden van vertrouwens wachtwoorden, het verzamelen van vertrouwens informatie en het controleren van vertrouwens relaties door interactie met het LSA-proces en het TDO.

    Voor forest-vertrouwens relaties bevat de vertrouwens informatie de *FTInfo*-record (Forest Trust Information), die de set naam ruimten bevat die een vertrouwd forest claimt om te beheren, aantekeningen maken met een veld dat aangeeft of elke claim wordt vertrouwd door het vertrouwende forest.

* Verificatie: levert gebruikers referenties via een beveiligd kanaal naar een domein controller en retourneert de domein-Sid's en gebruikers rechten voor de gebruiker.

* Locatie van domein controller: helpt bij het vinden of zoeken van domein controllers in een domein of tussen domeinen.

* Pass-Through-validatie: referenties van gebruikers in andere domeinen worden verwerkt door Net Logon. Wanneer een vertrouwend domein de identiteit van een gebruiker moet verifiëren, worden de referenties van de gebruiker door middel van een net-aanmelding bij het vertrouwde domein door gegeven voor verificatie.

* Verificatie van het privilege Attribute Certificate (PAC): wanneer een server die gebruikmaakt van het Kerberos-protocol voor verificatie, de PAC in een service ticket moet verifiëren, verzendt de Pac over het beveiligde kanaal naar de domein controller voor verificatie.

### <a name="local-security-authority"></a>Lokale certificeringsinstantie

De lokale beveiligings autoriteit (LSA) is een beschermd subsysteem dat informatie over alle aspecten van lokale beveiliging op een systeem bijhoudt. LSA wordt gezamenlijk het lokale beveiligings beleid genoemd en biedt verschillende services voor de vertaling tussen namen en id's.

Het beveiligings subsysteem LSA biedt services in zowel de kernelmodus als de gebruikers modus om de toegang tot objecten te valideren, gebruikers bevoegdheden te controleren en controle berichten te genereren. LSA is verantwoordelijk voor het controleren van de geldigheid van alle sessie tickets die worden gepresenteerd door services in vertrouwde of niet-vertrouwde domeinen.

### <a name="management-tools"></a>Beheerhulpprogramma's

Beheerders kunnen *Active Directory domeinen en vertrouwens relaties*, *netdom* en *nltest* gebruiken om vertrouwens relaties weer te geven, te maken, te verwijderen of te wijzigen.

* *Active Directory domeinen en vertrouwens relaties* is de micro soft Management Console (MMC) die wordt gebruikt voor het beheren van vertrouwens relaties tussen domeinen, functionaliteits niveaus van domeinen en forests en User Principal name achtervoegsels.
* De opdracht regel Programma's *netdom* en *nltest* kunnen worden gebruikt om vertrouwens relaties te zoeken, weer te geven, te maken en te beheren. Deze hulpprogram ma's communiceren rechtstreeks met de LSA-instantie op een domein controller.

## <a name="next-steps"></a>Volgende stappen

Zie [Hoe kunnen forest-vertrouwens relaties werken in Azure AD DS?][concepts-trust] voor meer informatie over resource forests.

Zie [een door Azure AD DS beheerd domein maken en configureren][tutorial-create-advanced]om aan de slag te gaan met het maken van een beheerd domein met een bron-forest. U kunt vervolgens [een uitgaande forest-vertrouwens relatie maken met een on-premises domein][create-forest-trust].

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
