---
title: Problemen met self-service voor wacht woord opnieuw instellen terugschrijven-Azure Active Directory
description: Meer informatie over het oplossen van veelvoorkomende problemen en oplossingen voor het terugschrijven van wacht woord opnieuw instellen in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0620304de1866d24719b137836419502cd25bee9
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682234"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Problemen met het terugschrijven van wacht woord opnieuw instellen in Azure Active Directory

Met Azure Active Directory (Azure AD) self-service voor wachtwoord herstel (SSPR) kunnen gebruikers hun wacht woord opnieuw instellen in de Cloud. Wacht woord terugschrijven is een functie ingeschakeld met [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) waarmee wachtwoord wijzigingen in de cloud in realtime naar een bestaande on-premises Directory kunnen worden geschreven.

Als u problemen ondervindt met SSPR write-back, kunnen de volgende probleemoplossings stappen en veelvoorkomende fouten van pas komen. Als u het antwoord op uw probleem niet kunt vinden, [zijn onze ondersteunings teams altijd beschikbaar](#contact-microsoft-support) om u verder te helpen.

## <a name="troubleshoot-connectivity"></a>Problemen met verbindingen oplossen

Als u problemen ondervindt met het terugschrijven van wacht woorden voor Azure AD Connect, raadpleegt u de volgende stappen om het probleem te verhelpen. Als u uw service wilt herstellen, kunt u het beste de volgende stappen in de aangegeven volg orde uitvoeren:

* [Netwerk connectiviteit bevestigen](#confirm-network-connectivity)
* [De Azure AD Connect synchronisatie service opnieuw starten](#restart-the-azure-ad-connect-sync-service)
* [De functie voor het terugschrijven van wacht woorden uitschakelen en opnieuw inschakelen](#disable-and-re-enable-the-password-writeback-feature)
* [Installeer de nieuwste versie van Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Problemen met wacht woord terugschrijven oplossen](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Netwerk connectiviteit bevestigen

Het meest voorkomende fout punt is dat de firewall of Proxy poort of inactieve time-outs onjuist zijn geconfigureerd.

Voor Azure AD Connect versie *1.1.443.0* en hoger is *uitgaande https* -toegang vereist voor de volgende adressen:

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

Azure [gov-eind punten](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers):

* *\*. passwordreset.microsoftonline.us*
* *\*. servicebus.usgovcloudapi.net*

Zie de [lijst met IP-adresbereiken van Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653)als u meer granulariteit nodig hebt. Deze lijst wordt elke woensdag bijgewerkt en gaat in op de volgende maandag.

Raadpleeg de [vereisten voor connectiviteit voor Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md)voor meer informatie.

### <a name="restart-the-azure-ad-connect-sync-service"></a>De Azure AD Connect synchronisatie service opnieuw starten

Als u verbindings problemen of andere tijdelijke problemen met de service wilt oplossen, voert u de volgende stappen uit om de Azure AD Connect Sync-service opnieuw te starten:

1. Selecteer **Start** als beheerder op de server waarop Azure AD Connect wordt uitgevoerd.
1. Voer *Services. msc* in het zoek veld in en selecteer **Enter**.
1. Zoek naar de *Microsoft Azure AD Sync* -vermelding.
1. Klik met de rechter muisknop op het item service, selecteer **opnieuw opstarten** en wacht totdat de bewerking is voltooid.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="De Azure AD Sync-service opnieuw starten met de gebruikers interface" border="false":::

Met deze stappen wordt uw verbinding met Azure AD opnieuw tot stand gebracht en worden uw verbindings problemen opgelost.

Als het probleem niet wordt opgelost door de Azure AD Connect Sync-service opnieuw te starten, probeert u de functie voor het terugschrijven van wacht woorden in de volgende sectie uit te scha kelen en opnieuw in te scha kelen.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>De functie voor het terugschrijven van wacht woorden uitschakelen en opnieuw inschakelen

Als u problemen wilt oplossen, voert u de volgende stappen uit om de functie voor het terugschrijven van wacht woorden uit te scha kelen en opnieuw in te scha kelen:

1. Als beheerder op de server waarop Azure AD Connect wordt uitgevoerd, opent u de **wizard Azure AD Connect configuratie**.
1. In **verbinding maken met Azure AD** voert u de referenties in van de globale beheerder van Azure AD.
1. In **verbinding maken met AD DS** voert u uw lokale Active Directory Domain Services beheerders referenties in.
1. Selecteer de knop **volgende** om **uw gebruikers uniek te identificeren**.
1. Schakel in **optionele functies** het selectie vakje **wacht woord terugschrijven** uit.
1. Selecteer **volgende** in de resterende dialoog pagina's zonder dat u iets hoeft te wijzigen totdat u de pagina **gereed om te configureren** hebt.
1. Controleer of de **pagina gereed voor configuratie** de optie voor het *terugschrijven van wacht woorden* als *uitgeschakeld* wordt weer gegeven. Selecteer de knop groen **configureren** om uw wijzigingen door te voeren.
1. Als u **klaar bent**, wist u de optie **Nu synchroniseren** en selecteert u vervolgens **volt ooien** om de wizard te sluiten.
1. Open de **wizard Azure AD Connect configuratie** opnieuw.
1. Herhaal stap 2-8, deze keer dat u de optie voor het *terugschrijven van wacht woorden* selecteert op de pagina **optionele functies** om de service opnieuw in te scha kelen.

Met deze stappen wordt uw verbinding met Azure AD opnieuw tot stand gebracht en worden uw verbindings problemen opgelost.

Als het uitschakelen en vervolgens opnieuw inschakelen van de functie voor het terugschrijven van wacht woorden het probleem niet wordt opgelost, installeert u Azure AD Connect opnieuw in de volgende sectie.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installeer de nieuwste versie van Azure AD Connect

Als Azure AD Connect opnieuw wordt geïnstalleerd, kunt u problemen met de configuratie en connectiviteit tussen Azure AD en uw lokale Active Directory Domain Services omgeving oplossen. U wordt aangeraden deze stap alleen uit te voeren nadat u de voor gaande stappen hebt uitgevoerd om connectiviteit te controleren en problemen op te lossen.

> [!WARNING]
> Als u de out-of-Box Sync-regels hebt aangepast, *maakt u er een back-up van voordat u doorgaat met de upgrade en implementeert u deze hand matig opnieuw nadat u klaar bent.*

1. Down load de nieuwste versie van Azure AD Connect van het [micro soft Download centrum](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Als u Azure AD Connect al hebt geïnstalleerd, voert u een in-place upgrade uit om de installatie van Azure AD Connect bij te werken naar de nieuwste versie.

    Voer het gedownloade pakket uit en volg de instructies op het scherm om Azure AD Connect bij te werken.

Met deze stappen wordt de verbinding met Azure AD opnieuw tot stand gebracht en worden de verbindings problemen opgelost.

Als het probleem niet wordt opgelost door de nieuwste versie van de Azure AD Connect server te installeren, kunt u proberen om het terugschrijven van wacht woorden uit te scha kelen en vervolgens opnieuw in te scha kelen als laatste stap nadat u de nieuwste versie hebt geïnstalleerd.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Controleren of Azure AD Connect over de vereiste machtigingen beschikt

Azure AD Connect moet AD DS machtiging **wacht woord opnieuw instellen** zijn vereist om wacht woord terugschrijven uit te voeren. Als u wilt controleren of Azure AD Connect de vereiste machtiging heeft voor een bepaalde on-premises AD DS gebruikers account, gebruikt u de Windows-functie **effectief machtigingen** :

1. Meld u aan bij de Azure AD Connect-server en start de **Synchronization Service Manager** door   >  **synchronisatie service** starten te selecteren.
1. Selecteer op het tabblad **connectors** de on-premises **Active Directory Domain Services** -connector en selecteer vervolgens **Eigenschappen**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="Synchronization Service Manager waarin wordt weer gegeven hoe u eigenschappen kunt bewerken" border="false":::
  
1. In het pop-upvenster selecteert **u verbinding maken met Active Directory forest** en noteert u de eigenschap **gebruikers naam** . Deze eigenschap is het AD DS account dat door Azure AD Connect wordt gebruikt voor het uitvoeren van Directory synchronisatie.

    Als Azure AD Connect wacht woord terugschrijven wilt uitvoeren, moet de AD DS-account de machtiging wacht woord opnieuw instellen hebben. U controleert de machtigingen voor dit gebruikers account in de volgende stappen.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="Het Active Directory gebruikers account van de synchronisatie service zoeken" border="false":::
  
1. Meld u aan bij een on-premises domein controller en start de toepassing **Active Directory gebruikers en computers** .
1. Selecteer **weer gave** en zorg ervoor dat de optie **geavanceerde functies** is ingeschakeld.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Active Directory gebruikers en computers geavanceerde functies weer geven" border="false":::
  
1. Zoek het AD DS gebruikers account dat u wilt controleren. Klik met de rechter muisknop op de account naam en selecteer **Eigenschappen**.  
1. In het pop-upvenster gaat u naar het tabblad **beveiliging** en selecteert u **Geavanceerd**.  
1. Ga in het pop-upvenster **Geavanceerde beveiligings instellingen voor beheerder** naar het tabblad **effectief toegang** .
1. Kies **een gebruiker selecteren**, selecteer het AD DS account dat wordt gebruikt door Azure AD Connect en selecteer vervolgens **actieve toegang weer geven**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="Tabblad effectief toegang met het synchronisatie account" border="false":::
  
1. Schuif naar beneden en zoek naar **wacht woord opnieuw instellen**. Als de vermelding een vinkje heeft, is het AD DS-account gemachtigd om het wacht woord van de geselecteerde Active Directory gebruikers account opnieuw in te stellen.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Controleren of de synchronisatie account de machtiging wacht woord opnieuw instellen heeft" border="false":::

## <a name="common-password-writeback-errors"></a>Veelvoorkomende fouten bij het terugschrijven van wacht woorden

De volgende specifieke problemen kunnen zich voordoen bij het terugschrijven van wacht woorden. Als u een van deze fouten hebt, raadpleegt u de voorgestelde oplossing en controleert u of het terugschrijven van wacht woorden correct werkt.

| Fout | Oplossing |
| --- | --- |
| De service voor het opnieuw instellen van het wacht woord wordt niet on-premises gestart. Fout 6800 wordt weer gegeven in het toepassings gebeurtenis logboek van de Azure AD Connect computer. <br> <br> Na onboarding, federatieve, Pass-Through-verificatie of gebruikers met een wacht woord-hash-synchronisatie kunnen hun wacht woorden niet opnieuw instellen. | Wanneer het terugschrijven van wacht woorden is ingeschakeld, roept de synchronisatie-engine de terugschrijf bibliotheek aan om de configuratie (onboarding) uit te voeren door te communiceren met de Cloud service voor onboarding. Er zijn fouten opgetreden tijdens het voorbereiden of tijdens het starten van het eind punt van de Windows Communication Foundation (WCF) voor het terugschrijven van wacht woorden met fouten in het gebeurtenis logboek op uw Azure AD Connect computer. <br> <br> Bij het opnieuw opstarten van de Azure AD Sync-Service (ADSync), als write-back is geconfigureerd, wordt het WCF-eind punt gestart. Maar als het starten van het eind punt mislukt, registreren we gebeurtenis 6800 en laten we de synchronisatie service starten. De aanwezigheid van deze gebeurtenis betekent dat het eind punt voor het terugschrijven van het wacht woord niet kan worden gestart. Details van gebeurtenis logboeken voor deze gebeurtenis 6800, samen met de vermeldingen in het gebeurtenis logboek gegenereerd door het onderdeel PasswordResetService, geven aan waarom u het eind punt niet kunt starten. Controleer deze gebeurtenis logboek fouten en probeer de Azure AD Connect opnieuw te starten als het terugschrijven van wacht woorden nog niet werkt. Als het probleem zich blijft voordoen, probeert u het terugschrijven van wacht woorden uit te scha kelen en opnieuw in te scha kelen.
| Wanneer een gebruiker probeert een wacht woord opnieuw in te stellen of een account wilt ontgrendelen waarvoor het terugschrijven van wacht woorden is ingeschakeld, mislukt de bewerking. <br> <br> Daarnaast ziet u een gebeurtenis in het gebeurtenis logboek van Azure AD Connect dat bevat: ' Synchronization engine heeft een fout verzonden HR = 800700CE, bericht = de bestands naam of-extensie is te lang ' nadat de ontgrendelings bewerking is uitgevoerd. | Zoek het Active Directory-account voor Azure AD Connect en stel het wacht woord opnieuw in, zodat het Maxi maal 256 tekens bevat. Open vervolgens de **synchronisatie service** vanuit het menu **Start** . Blader naar **connectors** en zoek de **Active Directory-Connector**. Selecteer deze en selecteer vervolgens **Eigenschappen**. Blader naar de pagina **referenties** en voer het nieuwe wacht woord in. Selecteer **OK** om de pagina te sluiten. |
| Bij de laatste stap van het Azure AD Connect-installatie proces ziet u een fout bericht dat aangeeft dat het terugschrijven van wacht woorden niet kan worden geconfigureerd. <br> <br> Het gebeurtenis logboek van Azure AD Connect toepassing bevat fout 32009 met de tekst ' fout bij het ophalen van auth-token '. | Deze fout treedt op in de volgende twee gevallen: <br><ul><li>U hebt een onjuist wacht woord opgegeven voor het globale beheerders account dat aan het begin van het Azure AD Connect installatie proces is opgegeven.</li><li>U hebt geprobeerd een federatieve gebruiker te gebruiken voor het account van de globale beheerder die aan het begin van het Azure AD Connect installatie proces is opgegeven.</li></ul> Als u dit probleem wilt verhelpen, moet u ervoor zorgen dat u geen federatief account gebruikt voor de globale beheerder die u aan het begin van het installatie proces hebt opgegeven en dat het opgegeven wacht woord juist is. |
| Het gebeurtenis logboek van de Azure AD Connect-machine bevat fout 32002 die wordt gegenereerd door het uitvoeren van PasswordResetService. <br> <br> De fout leest: ' fout bij het maken van verbinding met ServiceBus. De token provider kan geen beveiligings token opgeven. " | Uw on-premises omgeving kan geen verbinding maken met het Azure Service Bus-eind punt in de Cloud. Deze fout wordt meestal veroorzaakt door een firewall regel die een uitgaande verbinding met een bepaalde poort of een webadres blokkeert. Zie [connectiviteits vereisten](../hybrid/how-to-connect-install-prerequisites.md) voor meer informatie. Nadat u deze regels hebt bijgewerkt, moet u de Azure AD Connect-server en het terugschrijven van het wacht woord opnieuw starten. |
| Na enige tijd, federatieve, Pass-Through-verificatie of wacht woord-hash-gesynchroniseerde gebruikers kunnen hun wacht woorden niet opnieuw instellen. | In sommige zeldzame gevallen kan de service voor het terugschrijven van wacht woorden niet opnieuw worden opgestart wanneer Azure AD Connect opnieuw is opgestart. In dergelijke gevallen controleert u eerst of het terugschrijven van wacht woorden on-premises is ingeschakeld. U kunt controleren met behulp van de Azure AD Connect wizard of Power shell. Als de functie lijkt te zijn ingeschakeld, kunt u de functie opnieuw proberen in of uit te scha kelen. Als deze probleemoplossings stap niet werkt, probeert u een volledige installatie van Azure AD Connect en opnieuw te installeren. |
| Federatieve, Pass-Through-verificatie of gebruikers met een wacht woord-hash-synchronisatie zien een fout na het verzenden van hun wacht woord. De fout geeft aan dat er een service probleem is opgetreden. <br ><br> Naast dit probleem kan tijdens het opnieuw instellen van het wacht woord een fout melding krijgen dat de beheer agent geen toegang meer heeft in uw on-premises gebeurtenis Logboeken. | Als deze fouten worden weer gegeven in uw gebeurtenis logboek, controleert u of het ADMA-account (Active Directory Management Agent) dat is opgegeven in de wizard op het moment van de configuratie, de benodigde machtigingen heeft voor het terugschrijven van wacht woorden. <br> <br> Nadat deze machtiging is gegeven, kan het tot één uur duren voordat de machtigingen worden trickle via de `sdprop` achtergrond taak op de domein controller (DC). <br> <br> Om het wacht woord opnieuw in te stellen, moet de machtiging worden gestempeld op het security descriptor van het gebruikers object waarvan het wacht woord opnieuw wordt ingesteld. Totdat deze machtiging wordt weer gegeven op het gebruikers object, blijft het opnieuw instellen van wacht woorden mislukken met een bericht dat de toegang is geweigerd. |
| Federatieve, Pass-Through-verificatie of gebruikers met een wacht woord-hash-synchronisatie die proberen hun wacht woord opnieuw in te stellen. De fout geeft aan dat er een service probleem is opgetreden. <br> <br> Naast dit probleem wordt tijdens het opnieuw instellen van het wacht woord een fout weer gegeven in de gebeurtenis logboeken van de Azure AD Connect-service die aangeeft dat een object niet kan worden gevonden. | Deze fout geeft meestal aan dat het gebruikers object niet kan worden gevonden in de ruimte van de Azure AD-connector of in het gekoppelde niet-geplaatste (MV) of Azure AD connector-object. <br> <br> Om dit probleem op te lossen, moet u ervoor zorgen dat de gebruiker inderdaad van on-premises naar Azure AD is gesynchroniseerd via het huidige exemplaar van Azure AD Connect en de status van de objecten in de connector ruimten en MV te controleren. Controleer of het AD CS-object (Active Directory Certificate Services) met het MV-object is verbonden via de regel ' Microsoft.InfromADUserAccountEnabled.xxx '.|
| Federatieve, Pass-Through-verificatie of gebruikers met een wacht woord-hash-synchronisatie zien een fout nadat ze hun wacht woord hebben ingediend. De fout geeft aan dat er een service probleem is opgetreden. <br> <br> Naast dit probleem wordt tijdens het opnieuw instellen van het wacht woord een fout weer gegeven in de gebeurtenis logboeken van de Azure AD Connect-service. Dit geeft aan dat er een fout is opgetreden bij het uitvoeren van meerdere overeenkomsten. | Dit geeft aan dat de synchronisatie-engine heeft gedetecteerd dat het MV-object is verbonden met meer dan één AD CS-object via ' Microsoft.InfromADUserAccountEnabled.xxx '. Dit betekent dat de gebruiker in meer dan één forest een ingeschakeld account heeft. Dit scenario wordt niet ondersteund voor het terugschrijven van wacht woorden. |
| Er is een wachtwoord bewerking mislukt met een configuratie fout. Het gebeurtenis logboek van de toepassing bevat Azure AD Connect fout 6329 met de tekst ' 0x8023061f (de bewerking is mislukt omdat wachtwoord synchronisatie niet is ingeschakeld op deze beheer agent) '. | Deze fout treedt op als de Azure AD Connect configuratie wordt gewijzigd om een nieuw Active Directory forest toe te voegen (of om een bestaand forest te verwijderen en te lezen) nadat de functie voor het terugschrijven van wacht woorden al is ingeschakeld. Wacht woord bewerkingen voor gebruikers in deze onlangs toegevoegde forests mislukken. U kunt het probleem oplossen door de functie voor het terugschrijven van wacht woorden uit te scha kelen en opnieuw in te scha kelen nadat de wijzigingen in de configuratie van de forest zijn voltooid. |

## <a name="password-writeback-event-log-error-codes"></a>Fout codes voor het terugschrijven van wacht woorden

Een best practice bij het oplossen van problemen met wacht woord terugschrijven is het controleren van het toepassings gebeurtenis logboek op uw Azure AD Connect computer. Dit gebeurtenis logboek bevat gebeurtenissen van twee bronnen voor het terugschrijven van wacht woorden. In de *PasswordResetService* -bron worden de bewerkingen en problemen beschreven die betrekking hebben op de werking van het terugschrijven van wacht woorden. In de *ADSync* -bron worden de bewerkingen en problemen met betrekking tot het instellen van wacht woorden in uw Active Directory Domain Services omgeving beschreven.

### <a name="if-the-source-of-the-event-is-adsync"></a>Als de bron van de gebeurtenis ADSync is

| Code | Naam of bericht | Beschrijving |
| --- | --- | --- |
| 6329 | Afwijzen: MMS (4924) 0x80230619: ' door een beperking wordt voor komen dat het wacht woord wordt gewijzigd in de huidige opgegeven. ' | Deze gebeurtenis treedt op wanneer de service voor het terugschrijven van wacht woorden probeert in te stellen op uw lokale adres lijst die niet voldoet aan de vereisten voor wachtwoord duur, geschiedenis, complexiteit of filtering van het domein. <br> <br> Als u een minimale wachtwoord duur hebt en het wacht woord onlangs hebt gewijzigd binnen dat venster, kunt u het wacht woord niet meer wijzigen tot de opgegeven leeftijd in uw domein is bereikt. Voor test doeleinden moet de minimale leeftijd worden ingesteld op 0. <br> <br> Als u vereisten voor wachtwoord geschiedenis hebt ingeschakeld, moet u een wacht woord selecteren dat niet is gebruikt in de afgelopen *N* keer, waarbij *N* de instelling voor wachtwoord geschiedenis is. Als u een wacht woord selecteert dat in de afgelopen *N* keer is gebruikt, ziet u een fout in dit geval. Voor test doeleinden moet de wachtwoord geschiedenis worden ingesteld op 0. <br> <br> Als u vereisten voor wachtwoord complexiteit hebt, worden deze allemaal afgedwongen wanneer de gebruiker een wacht woord probeert te wijzigen of opnieuw in te stellen. <br> <br> Als wachtwoord filters zijn ingeschakeld en een gebruiker een wacht woord selecteert dat niet voldoet aan de filter criteria, mislukt de bewerking voor opnieuw instellen of wijzigen. |
| 6329 | MMS (3040): admaexport. cpp (2837): de server bevat niet het besturings element LDAP-wachtwoord beleid. | Dit probleem treedt op als LDAP_SERVER_POLICY_HINTS_OID besturings element (1.2.840.113556.1.4.2066) niet is ingeschakeld op de Dc's. Als u de functie voor het terugschrijven van wacht woorden wilt gebruiken, moet u het besturings element inschakelen. Hiertoe moet de Dc's zich op Windows Server 2008R2 of later bevindt. |
| HR 8023042 | De synchronisatie-engine heeft een fout verzonden HR = 80230402, bericht = er is een poging gedaan om een object op te halen omdat er dubbele vermeldingen met hetzelfde anker zijn. | Deze fout treedt op wanneer dezelfde gebruikers-ID is ingeschakeld in meerdere domeinen. Een voor beeld is als u het account en de resource forests synchroniseert en dezelfde gebruikers-ID aanwezig en ingeschakeld hebt in elk forest. <br> <br> Deze fout kan ook optreden als u een niet-uniek anker kenmerk gebruikt, zoals een alias of UPN, en twee gebruikers hetzelfde anker kenmerk delen. <br> <br> Om dit probleem op te lossen, moet u ervoor zorgen dat u geen dubbele gebruikers in uw domeinen hebt en dat u voor elke gebruiker een uniek anker kenmerk gebruikt. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Als de bron van de gebeurtenis PasswordResetService is

| Code | Naam of bericht | Beschrijving |
| --- | --- | --- |
| 31001 | PasswordResetStart | Deze gebeurtenis geeft aan dat de on-premises service een aanvraag voor het opnieuw instellen van een wacht woord heeft gedetecteerd voor een federatieve, Pass-Through-verificatie of gebruiker met hash-synchronisatie met wacht woord afkomstig uit de Cloud. Deze gebeurtenis is de eerste gebeurtenis in elke terugschrijf bewerking van een wacht woord. |
| 31002 | PasswordResetSuccess | Deze gebeurtenis geeft aan dat een gebruiker een nieuw wacht woord heeft geselecteerd tijdens een bewerking voor het opnieuw instellen van een wacht woord. We hebben vastgesteld dat dit wacht woord voldoet aan de vereisten voor het bedrijfs wachtwoord. Het wacht woord is teruggeschreven naar de lokale Active Directory-omgeving. |
| 31003 | PasswordResetFail | Deze gebeurtenis geeft aan dat een gebruiker een wacht woord heeft geselecteerd en dat het wacht woord is aangekomen in de on-premises omgeving. Er is een fout opgetreden bij het instellen van het wacht woord in de lokale Active Directory omgeving. Deze fout kan verschillende oorzaken hebben: <br><ul><li>Het wacht woord van de gebruiker voldoet niet aan de vereisten voor leeftijd, geschiedenis, complexiteit of filter voor het domein. Om dit probleem op te lossen, maakt u een nieuw wacht woord.</li><li>Het ADMA-service account beschikt niet over de juiste machtigingen om het nieuwe wacht woord voor het desbetreffende gebruikers account in te stellen.</li><li>Het account van de gebruiker bevindt zich in een beveiligde groep, zoals een groep voor het domein of een ondernemings Administrator, waardoor wachtwoord sets niet worden toegestaan.</li></ul>|
| 31004 | OnboardingEventStart | Deze gebeurtenis treedt op als u het terugschrijven van wacht woorden met Azure AD Connect inschakelt en uw organisatie is gestart met de webservice voor het terugschrijven van wacht woorden. |
| 31005 | OnboardingEventSuccess | Deze gebeurtenis geeft aan dat het voorbereidings proces is geslaagd en dat de mogelijkheid voor het terugschrijven van wacht woorden gereed is voor gebruik. |
| 31006 | ChangePasswordStart | Deze gebeurtenis geeft aan dat de on-premises service een wachtwoord wijzigings aanvraag heeft gedetecteerd voor een federatieve, Pass-Through-verificatie of door wacht woord-hash gesynchroniseerde gebruiker die afkomstig is uit de Cloud. Deze gebeurtenis is de eerste gebeurtenis in elke terugschrijf bewerking voor wacht woorden. |
| 31007 | ChangePasswordSuccess | Deze gebeurtenis geeft aan dat een gebruiker tijdens het wijzigen van het wacht woord een nieuw wacht woord heeft geselecteerd, we hebben vastgesteld dat het wacht woord voldoet aan de vereisten voor het wacht woord van het bedrijf en dat het wacht woord is teruggeschreven naar de lokale Active Directory-omgeving. |
| 31008 | ChangePasswordFail | Deze gebeurtenis geeft aan dat een gebruiker een wacht woord heeft geselecteerd en dat het wacht woord is aangekomen bij de on-premises omgeving, maar wanneer het wacht woord in de lokale Active Directory omgeving is ingesteld, is er een fout opgetreden. Deze fout kan verschillende oorzaken hebben: <br><ul><li>Het wacht woord van de gebruiker voldoet niet aan de vereisten voor leeftijd, geschiedenis, complexiteit of filter voor het domein. Om dit probleem op te lossen, maakt u een nieuw wacht woord.</li><li>Het ADMA-service account beschikt niet over de juiste machtigingen om het nieuwe wacht woord voor het desbetreffende gebruikers account in te stellen.</li><li>Het account van de gebruiker bevindt zich in een beveiligde groep, zoals domein-of ondernemings Administrators, waardoor wachtwoord sets niet worden toegestaan.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | De on-premises service heeft een aanvraag voor het opnieuw instellen van een wacht woord gedetecteerd voor een federatieve, Pass-Through-verificatie of gebruiker met wacht woord-hash-Synchronized van de beheerder namens een gebruiker. Deze gebeurtenis is de eerste gebeurtenis in elke terugschrijf bewerking die door een beheerder wordt gestart. |
| 31010 | ResetUserPasswordByAdminSuccess | De beheerder heeft een nieuw wacht woord geselecteerd tijdens een door de beheerder geïnitieerde bewerking voor het opnieuw instellen van een wacht woord. We hebben vastgesteld dat dit wacht woord voldoet aan de vereisten voor het bedrijfs wachtwoord. Het wacht woord is teruggeschreven naar de lokale Active Directory-omgeving. |
| 31011 | ResetUserPasswordByAdminFail | De beheerder heeft een wacht woord geselecteerd namens een gebruiker. Het wacht woord is aangekomen in de on-premises omgeving. Er is een fout opgetreden bij het instellen van het wacht woord in de lokale Active Directory omgeving. Deze fout kan verschillende oorzaken hebben: <br><ul><li>Het wacht woord van de gebruiker voldoet niet aan de vereisten voor leeftijd, geschiedenis, complexiteit of filter voor het domein. Probeer het opnieuw met een nieuw wacht woord om dit probleem op te lossen.</li><li>Het ADMA-service account beschikt niet over de juiste machtigingen om het nieuwe wacht woord voor het desbetreffende gebruikers account in te stellen.</li><li>Het account van de gebruiker bevindt zich in een beveiligde groep, zoals domein-of ondernemings Administrators, waardoor wachtwoord sets niet worden toegestaan.</li></ul>  |
| 31012 | OffboardingEventStart | Deze gebeurtenis treedt op als u het terugschrijven van wacht woorden met Azure AD Connect uitschakelt en aangeeft dat uw organisatie is geoffboarding tot de webservice voor het terugschrijven van wacht woorden. |
| 31013| OffboardingEventSuccess| Deze gebeurtenis geeft aan dat het offboarding-proces is geslaagd en dat de mogelijkheid voor het terugschrijven van wacht woorden is uitgeschakeld. |
| 31014| OffboardingEventFail| Deze gebeurtenis geeft aan dat het offboarding-proces niet is geslaagd. Dit kan worden veroorzaakt door een machtigings fout op het Cloud-of on-premises beheerders account dat is opgegeven tijdens de configuratie. De fout kan ook optreden als u probeert een federatieve globale Cloud beheerder te gebruiken bij het uitschakelen van het terugschrijven van wacht woorden. U kunt dit probleem oplossen door uw beheerders machtigingen te controleren en ervoor te zorgen dat u geen federatief account gebruikt tijdens het configureren van de functie voor het terugschrijven van wacht woorden.|
| 31015| WriteBackServiceStarted| Deze gebeurtenis geeft aan dat de service voor het terugschrijven van wacht woorden is gestart. Het is gereed om aanvragen voor wachtwoord beheer te accepteren vanuit de Cloud.|
| 31016| WriteBackServiceStopped| Deze gebeurtenis geeft aan dat de service voor het terugschrijven van wacht woorden is gestopt. Aanvragen voor wachtwoord beheer vanuit de Cloud zijn niet geslaagd.|
| 31017| AuthTokenSuccess| Deze gebeurtenis geeft aan dat er een autorisatie token is opgehaald voor de globale beheerder die tijdens Azure AD Connect Setup is opgegeven om het offboarding-of onboarding-proces te starten.|
| 31018| KeyPairCreationSuccess| Deze gebeurtenis geeft aan dat de wachtwoord versleutelings sleutel is gemaakt. Deze sleutel wordt gebruikt om wacht woorden uit de cloud te versleutelen die naar uw on-premises omgeving moeten worden verzonden.|
| 31034| ServiceBusListenerError| Deze gebeurtenis geeft aan dat er een fout is opgetreden bij het verbinding maken met de Service Bus-listener van uw Tenant. Als het fout bericht ' het externe certificaat is ongeldig ' bevat, controleert u of uw Azure AD Connect server beschikt over alle vereiste basis certificerings instanties, zoals beschreven in wijzigingen in het [Azure TLS-certificaat](../../security/fundamentals/tls-certificate-changes.md). |
| 32000| UnknownError| Deze gebeurtenis geeft aan dat er een onbekende fout is opgetreden tijdens een wachtwoord beheer bewerking. Bekijk de uitzonderings tekst in de gebeurtenis voor meer informatie. Als u problemen ondervindt, kunt u proberen het terugschrijven van wacht woorden uit te scha kelen en opnieuw in te scha kelen. Als dit niet helpt, voegt u een kopie van uw gebeurtenis logboek toe, samen met de tracerings-ID die is opgegeven bij het openen van een ondersteunings aanvraag.|
| 32001| ServiceError| Deze gebeurtenis geeft aan dat er een fout is opgetreden bij het verbinding maken met de Cloud-service voor wachtwoord herstel. Deze fout treedt doorgaans op wanneer de on-premises service geen verbinding kan maken met de webservice voor het opnieuw instellen van het wacht woord.|
| 32002| ServiceBusError| Deze gebeurtenis geeft aan dat er een fout is opgetreden bij het verbinding maken met het Service Bus exemplaar van uw Tenant. Dit kan gebeuren als u uitgaande verbindingen in uw on-premises omgeving blokkeert. Controleer de firewall om ervoor te zorgen dat u verbindingen toestaat via TCP 443 en naar https://ssprdedicatedsbprodncu.servicebus.windows.net , en probeer het opnieuw. Als u nog steeds problemen ondervindt, kunt u proberen om wacht woord terugschrijven uit te scha kelen en opnieuw in te scha kelen.|
| 32003| InPutValidationError| Deze gebeurtenis geeft aan dat de invoer die is door gegeven aan de API van ons Web Service ongeldig is. Probeer de bewerking opnieuw uit te proberen.|
| 32004| DecryptionError| Deze gebeurtenis geeft aan dat er een fout is opgetreden bij het ontsleutelen van het wacht woord dat is ontvangen van de Cloud. Dit kan worden veroorzaakt door een niet-versleutelings sleutel tussen de Cloud service en uw on-premises omgeving. U kunt dit probleem oplossen door het terugschrijven van wacht woorden in uw on-premises omgeving uit te scha kelen en opnieuw in te scha kelen.|
| 32005| ConfigurationError| Tijdens het voorbereiden slaan we Tenant-specifieke informatie op in een configuratie bestand in uw on-premises omgeving. Deze gebeurtenis geeft aan dat er een fout is opgetreden bij het opslaan van dit bestand of dat toen de service werd gestart, er een fout is opgetreden bij het lezen van het bestand. U kunt dit probleem verhelpen door het uitschakelen en vervolgens opnieuw inschakelen van wacht woord terugschrijven in te scha kelen voor een herschrijf proces van het configuratie bestand.|
| 32007| OnBoardingConfigUpdateError| Tijdens het voorbereiden verzenden we gegevens vanuit de Cloud naar de on-premises service voor het opnieuw instellen van het wacht woord. Deze gegevens worden vervolgens naar een in-Memory bestand geschreven voordat het naar de synchronisatie service wordt verzonden om veilig te worden opgeslagen op schijf. Deze gebeurtenis geeft aan dat er een probleem is met het schrijven of bijwerken van de gegevens in het geheugen. U kunt dit probleem oplossen door het uitschakelen en vervolgens opnieuw inschakelen van wacht woord terugschrijven in te scha kelen om dit configuratie bestand te herstellen.|
| 32008| ValidationError| Deze gebeurtenis geeft aan dat er een ongeldig antwoord is ontvangen van de webservice voor het opnieuw instellen van het wacht woord. U kunt dit probleem oplossen door het uitschakelen en vervolgens opnieuw inschakelen van wacht woord terugschrijven in te scha kelen.|
| 32009| AuthTokenError| Deze gebeurtenis geeft aan dat er geen autorisatie token kan worden opgehaald voor het globale beheerders account dat is opgegeven tijdens de installatie van Azure AD Connect. Deze fout kan worden veroorzaakt door een onjuiste gebruikers naam of wacht woord dat is opgegeven voor het globale beheerders account. Deze fout kan ook optreden als het globale Administrator-account dat is opgegeven, federatief is. U kunt dit probleem oplossen door de configuratie opnieuw uit te voeren met de juiste gebruikers naam en wacht woord en ervoor te zorgen dat de beheerder een beheerd (alleen-Cloud of met een wacht woord gesynchroniseerd)-account is.|
| 32010| CryptoError| Deze gebeurtenis geeft aan dat er een fout is opgetreden bij het genereren van de wachtwoord versleutelings sleutel of het ontsleutelen van een wacht woord dat binnenkomt bij de Cloud service. Deze fout duidt mogelijk op een probleem met uw omgeving. Bekijk de details van uw gebeurtenis logboek voor meer informatie over het oplossen van dit probleem. U kunt ook proberen de service voor het terugschrijven van wacht woorden uit te scha kelen en opnieuw in te scha kelen.|
| 32011| OnBoardingServiceError| Deze gebeurtenis geeft aan dat de on-premises service niet goed kan communiceren met de webservice voor het opnieuw instellen van een wacht woord om het voorbereidings proces te initiëren. Dit kan gebeuren als gevolg van een firewall regel of als er een probleem is met het verkrijgen van een verificatie token voor uw Tenant. U kunt dit probleem oplossen door ervoor te zorgen dat de uitgaande verbindingen via TCP 443 en TCP 9350-9354 of niet worden geblokkeerd https://ssprdedicatedsbprodncu.servicebus.windows.net . Zorg er ook voor dat het Azure AD-beheerders account dat u gebruikt voor onboarding niet federatief is.|
| 32013| OffBoardingError| Deze gebeurtenis geeft aan dat de on-premises service niet goed kan communiceren met de webservice voor het opnieuw instellen van een wacht woord om het offboarding-proces te initiëren. Dit kan gebeuren als gevolg van een firewall regel of als er een probleem is met het verkrijgen van een autorisatie token voor uw Tenant. U kunt dit probleem oplossen door ervoor te zorgen dat u geen uitgaande verbindingen van 443 of naar blokkeert https://ssprdedicatedsbprodncu.servicebus.windows.net , en dat het Azure Active Directory beheerders account dat u gebruikt voor niet meer vrijgeven niet federatief is.|
| 32014| ServiceBusWarning| Deze gebeurtenis geeft aan dat we opnieuw proberen verbinding te maken met het Service Bus-exemplaar van uw Tenant. Onder normale omstandigheden zou dit geen bezorgdheid moeten zijn, maar als deze gebeurtenis vaak wordt weer geven, kunt u overwegen om uw netwerk verbinding te controleren op Service Bus, met name als het een verbinding met hoge latentie of lage band breedte is.|
| 32015| ReportServiceHealthError| Om de status van de service voor het terugschrijven van wacht woorden te controleren, verzenden we elke vijf minuten heartbeat-gegevens naar onze webservice voor het opnieuw instellen van een wacht woord. Deze gebeurtenis geeft aan dat er een fout is opgetreden bij het verzenden van deze status gegevens naar de Cloud-webservice. Deze status informatie omvat geen persoonlijke gegevens en is louter een heartbeat-en basis service statistieken, zodat we service status informatie kunnen bieden in de Cloud.|
| 33001| ADUnKnownError| Deze gebeurtenis geeft aan dat er een onbekende fout is geretourneerd door Active Directory. Controleer het gebeurtenis logboek van de Azure AD Connect-server op gebeurtenissen van de bron ADSync voor meer informatie.|
| 33002| ADUserNotFoundError| Deze gebeurtenis geeft aan dat de gebruiker die probeert het wacht woord opnieuw in te stellen of te wijzigen, niet is gevonden in de on-premises Directory. Deze fout kan optreden als de gebruiker lokaal is verwijderd, maar niet in de Cloud. Deze fout kan ook optreden als er een probleem is met de synchronisatie. Controleer de synchronisatie logboeken en de laatste details van de synchronisatie voor meer informatie.|
| 33003| ADMutliMatchError| Wanneer een wacht woord opnieuw instellen of een wijzigings aanvraag afkomstig is uit de Cloud, gebruiken we het Cloud anker dat is opgegeven tijdens het installatie proces van Azure AD Connect om te bepalen hoe die aanvraag moet worden gekoppeld aan een gebruiker in uw on-premises omgeving. Deze gebeurtenis geeft aan dat er twee gebruikers in uw on-premises Directory met hetzelfde Cloud anker kenmerk zijn gevonden. Controleer de synchronisatie logboeken en de laatste details van de synchronisatie voor meer informatie.|
| 33004| ADPermissionsError| Deze gebeurtenis geeft aan dat de ADMA-service account (Active Directory Management Agent) niet beschikt over de juiste machtigingen voor het account in kwestie om een nieuw wacht woord in te stellen. Zorg ervoor dat het ADMA-account in het forest van de gebruiker de wachtwoord machtigingen voor alle objecten in het forest opnieuw heeft ingesteld. Zie stap 4: de juiste Active Directory machtigingen instellen voor meer informatie over het instellen van machtigingen. Deze fout kan ook optreden als het kenmerk AdminCount van de gebruiker is ingesteld op 1.|
| 33005| ADUserAccountDisabled| Deze gebeurtenis geeft aan dat we hebben geprobeerd een wacht woord opnieuw in te stellen of te wijzigen voor een account dat on-premises is uitgeschakeld. Schakel het account in en probeer de bewerking opnieuw uit te voeren.|
| 33006| ADUserAccountLockedOut| Deze gebeurtenis geeft aan dat er een poging is gedaan om een wacht woord voor een account dat on-premises is vergrendeld, opnieuw in te stellen of te wijzigen. Vergren delingen kunnen zich voordoen wanneer een gebruiker de bewerking voor het wacht woord voor het wijzigen of opnieuw instellen te vaak in een korte periode heeft geprobeerd. Ontgrendel het account en voer de bewerking opnieuw uit.|
| 33007| ADUserIncorrectPassword| Deze gebeurtenis geeft aan dat de gebruiker een onjuist wacht woord heeft opgegeven tijdens het wijzigen van een wacht woord. Geef het juiste huidige wacht woord op en probeer het opnieuw.|
| 33008| ADPasswordPolicyError| Deze gebeurtenis treedt op wanneer de service voor het terugschrijven van wacht woorden probeert in te stellen op uw lokale adres lijst die niet voldoet aan de vereisten voor wachtwoord duur, geschiedenis, complexiteit of filtering van het domein. <br> <br> Als u een minimale wachtwoord duur hebt en het wacht woord onlangs hebt gewijzigd binnen dat venster, kunt u het wacht woord niet meer wijzigen tot de opgegeven leeftijd in uw domein is bereikt. Voor test doeleinden moet de minimale leeftijd worden ingesteld op 0. <br> <br> Als u vereisten voor wachtwoord geschiedenis hebt ingeschakeld, moet u een wacht woord selecteren dat niet is gebruikt in de afgelopen *N* keer, waarbij *N* de instelling voor wachtwoord geschiedenis is. Als u een wacht woord selecteert dat in de afgelopen *N* keer is gebruikt, ziet u een fout in dit geval. Voor test doeleinden moet de wachtwoord geschiedenis worden ingesteld op 0. <br> <br> Als u vereisten voor wachtwoord complexiteit hebt, worden deze allemaal afgedwongen wanneer de gebruiker een wacht woord probeert te wijzigen of opnieuw in te stellen. <br> <br> Als wachtwoord filters zijn ingeschakeld en een gebruiker een wacht woord selecteert dat niet voldoet aan de filter criteria, mislukt de bewerking voor opnieuw instellen of wijzigen.|
| 33009| ADConfigurationError| Deze gebeurtenis geeft aan dat er een probleem is opgetreden bij het schrijven van een wacht woord naar uw on-premises Directory vanwege een configuratie probleem met Active Directory. Controleer het toepassings gebeurtenis logboek van de Azure AD Connect computer op berichten van de ADSync-service voor meer informatie over welke fout is opgetreden.|

## <a name="azure-ad-forums"></a>Azure AD-forums

Als u algemene vragen hebt over Azure AD en self-service voor het opnieuw instellen van wacht woorden, kunt u de Community vragen om hulp op de [pagina micro soft Q&een vraag voor Azure Active Directory](/answers/topics/azure-active-directory.html). Leden van de Community bevatten technici, product managers, Mvp's en andere IT-professionals.

## <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Als u het antwoord op een probleem niet kunt vinden, zijn onze ondersteunings teams altijd beschikbaar om u verder te helpen.

Om u op de juiste wijze te helpen, vragen we u zoveel mogelijk details te verstrekken wanneer er een aanvraag wordt geopend. Deze informatie omvat het volgende:

* **Algemene beschrijving van de fout**: wat is de fout? Wat was het probleem dat is opgemerkt? Hoe kan de fout worden verreproduceerd? Geef zo veel mogelijk details op.
* **Pagina**: op welke pagina is u aangemeld toen u de fout hebt opgemerkt? Neem de URL op als u en een scherm opname van de pagina.
* **Ondersteunings code**: wat is de ondersteunings code die werd gegenereerd toen de gebruiker de fout zagte?
   * U kunt deze code vinden door de fout te reproduceren en vervolgens de koppeling **ondersteunings code** te selecteren onder aan het scherm en de ondersteunings technicus de GUID te sturen die het resultaat is.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="De ondersteunings code bevindt zich in de rechter benedenhoek van het browser venster.":::

  * Als u zich onder een pagina bevindt zonder een ondersteunings code, selecteert u F12 en zoekt u naar de SID en CID en verzendt u deze twee resultaten naar de ondersteunings technicus.
* **Datum, tijd en tijd zone**: bevatten de exacte datum en tijd *met de tijd zone* waarin de fout is opgetreden.
* **Gebruikers-id**: de gebruiker die de fout heeft gezien? Een voor beeld *is \@ contoso.com* van de gebruiker.
   * Is dit een federatieve gebruiker?
   * Is dit een Pass-Through-verificatie gebruiker?
   * Is dit een wacht woord-hash gesynchroniseerde gebruiker?
   * Is dit een alleen-Cloud gebruiker?
* **Licentie verlening**: er is een Azure AD-licentie toegewezen aan de gebruiker?
* **Logboek voor toepassings gebeurtenissen**: als u wacht woord terugschrijven gebruikt en de fout zich in uw on-premises infra structuur bevindt, moet u een gezipte kopie van het toepassings gebeurtenis logboek van de Azure AD Connect-server toevoegen.

## <a name="next-steps"></a>Volgende stappen

Zie hoe het werkt voor meer informatie over SSPR [: Azure AD selfservice voor wachtwoord herstel](concept-sspr-howitworks.md) en hoe werkt de [functie write-back via self-service voor wacht woord opnieuw instellen in azure AD?](concept-sspr-writeback.md).