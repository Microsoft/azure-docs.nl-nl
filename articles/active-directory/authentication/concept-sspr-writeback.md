---
title: On-premises write-back van wacht woord met selfservice voor wachtwoord herstel-Azure Active Directory
description: Meer informatie over het wijzigen van wacht woorden of het opnieuw instellen van gebeurtenissen in Azure Active Directory kan worden teruggeschreven naar een on-premises Directory-omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f416a23dbb47660097c41ada09c8c135434bcb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743646"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hoe werkt de write-back van self-service voor wacht woord opnieuw instellen in Azure Active Directory?

Met Azure Active Directory (Azure AD) self-service voor het opnieuw instellen van wacht woorden (SSPR) kunnen gebruikers hun wacht woord opnieuw instellen in de Cloud, maar de meeste bedrijven hebben ook een on-premises Active Directory Domain Services (AD DS)-omgeving waarin hun gebruikers zich bevinden. Wacht woord terugschrijven is een functie ingeschakeld met [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) waarmee wachtwoord wijzigingen in de cloud in realtime naar een bestaande on-premises Directory kunnen worden geschreven. In deze configuratie, wanneer gebruikers hun wacht woord wijzigen of opnieuw instellen met behulp van SSPR in de Cloud, worden de bijgewerkte wacht woorden ook teruggeschreven naar de on-premises AD DS omgeving

> [!IMPORTANT]
> In dit conceptuele artikel wordt uitgelegd hoe een beheerder de functie write-back van wacht woord opnieuw instellen werkt. Als u een eindgebruiker bent die al is geregistreerd voor self-service voor wachtwoordherstel en u weer toegang tot uw account wilt hebben, gaat u naar https://aka.ms/sspr.
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

Wacht woord terugschrijven wordt ondersteund in omgevingen die gebruikmaken van de volgende hybride identiteits modellen:

* [Synchronisatie van wachtwoord-hashes](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Pass-through-verificatie](../hybrid/how-to-connect-pta.md)
* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Het terugschrijven van wacht woorden biedt de volgende functies:

* **Afdwinging van het wachtwoord beleid van een on-premises Active Directory Domain Services (AD DS)**: wanneer een gebruiker het wacht woord opnieuw instelt, wordt gecontroleerd of het voldoet aan uw on-premises AD DS-beleid voordat het wordt doorgevoerd in die map. Deze controle omvat het controleren van de geschiedenis, complexiteit, leeftijd, wachtwoord filters en eventuele andere wachtwoord beperkingen die u in AD DS definieert.
* **Feedback over nul vertraging**: wacht woord terugschrijven is een synchrone bewerking. Gebruikers worden onmiddellijk op de hoogte gesteld als hun wacht woord niet voldoet aan het beleid of niet om een of andere reden kan worden gewijzigd.
* **Ondersteunt wachtwoord wijzigingen in het toegangs venster en Microsoft 365**: wanneer federatieve of wacht woord-hash gesynchroniseerde gebruikers hun verlopen of niet-verlopen wacht woorden kunnen wijzigen, worden deze wacht woorden teruggeschreven naar AD DS.
* **Biedt ondersteuning voor het terugschrijven van wacht woorden wanneer een beheerder deze opnieuw instelt op de Azure Portal**: wanneer een beheerder het wacht woord van een gebruiker opnieuw instelt in de [Azure Portal](https://portal.azure.com), wordt het wacht woord teruggeschreven naar on-premises als de gebruiker een federatieve of wacht woord-hash synchroniseert. Deze functionaliteit wordt momenteel niet ondersteund in de Office-beheer Portal.
* Geen **binnenkomende firewall regels vereist**: wacht woord terugschrijven gebruikt een Azure service bus relay als een onderliggend communicatie kanaal. Alle communicatie is uitgaand via poort 443.

> [!NOTE]
> Beheerders accounts die zich in beveiligde groepen in on-premises AD bevinden, kunnen worden gebruikt bij het terugschrijven van wacht woorden. Beheerders kunnen hun wacht woord wijzigen in de Cloud, maar kunnen geen wacht woord opnieuw instellen gebruiken om een verg eten wacht woord opnieuw in te stellen. Zie voor meer informatie over beveiligde groepen [beveiligde accounts en groepen in AD DS](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Voltooi de volgende zelf studie om aan de slag te gaan met SSPR write-back:

> [!div class="nextstepaction"]
> [Zelf studie: write-back van self-service voor wacht woord opnieuw instellen (SSPR) inschakelen](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Hoe wachtwoord terugschrijven werkt

Wanneer een federatieve of wacht woord-hash gesynchroniseerde gebruiker probeert om hun wacht woord opnieuw in te stellen of te wijzigen in de Cloud, worden de volgende acties uitgevoerd:

1. Er wordt een controle uitgevoerd om te zien welk type wacht woord de gebruiker heeft. Als het wacht woord on-premises wordt beheerd:
   * Er wordt een controle uitgevoerd om te zien of de Write-service actief is. Als dat het geval is, kan de gebruiker door gaan.
   * Als de Write-service niet beschikbaar is, wordt de gebruiker ervan op de hoogte gesteld dat het wacht woord niet kan worden hersteld.
1. Vervolgens geeft de gebruiker de juiste verificatie poorten door en bereikt de pagina **wacht woord opnieuw instellen** .
1. De gebruiker selecteert een nieuw wacht woord en bevestigt dit.
1. Wanneer de gebruiker **verzenden** selecteert, wordt het Lees bare wacht woord versleuteld met een symmetrische sleutel die is gemaakt tijdens het terugschrijf installatie proces.
1. Het versleutelde wacht woord is opgenomen in een Payload die via een HTTPS-kanaal wordt verzonden naar de Tenant-specifieke service bus relay (die tijdens het terugschrijf installatie proces is ingesteld). Deze relay wordt beveiligd door een wille keurig gegenereerd wacht woord dat alleen uw on-premises installatie kent.
1. Nadat het bericht de service bus heeft bereikt, wordt het eind punt voor het opnieuw instellen van het wacht woord automatisch geactiveerd en ziet u dat er een aanvraag voor opnieuw instellen in behandeling is.
1. De service zoekt vervolgens naar de gebruiker met behulp van het anker kenmerk Cloud. Deze zoek actie slaagt alleen als aan de volgende voor waarden wordt voldaan:

   * Het gebruikers object moet bestaan in de ruimte van de AD DS-connector.
   * Het gebruikers object moet worden gekoppeld aan het bijbehorende omgekeerde-object (MV).
   * Het gebruikers object moet worden gekoppeld aan het bijbehorende Azure AD connector-object.
   * De koppeling van het AD DS connector-object naar de MV moet de synchronisatie regel hebben `Microsoft.InfromADUserAccountEnabled.xxx` op de koppeling.

   Wanneer de oproep afkomstig is uit de Cloud, gebruikt de synchronisatie-engine het kenmerk **cloudAnchor** om het Azure AD connector Space-object op te zoeken. Vervolgens volgt u de koppeling terug naar het MV-object en volgt u de koppeling terug naar het AD DS-object. Omdat er meerdere AD DS objecten (meerdere forests) voor dezelfde gebruiker kunnen zijn, is de synchronisatie-engine afhankelijk van de `Microsoft.InfromADUserAccountEnabled.xxx` koppeling om de juiste te kiezen.

1. Nadat het gebruikers account is gevonden, wordt er een poging gedaan om het wacht woord rechtstreeks opnieuw in te stellen in het juiste AD DS-forest.
1. Als de wachtwoord set is geslaagd, wordt het wacht woord van de gebruiker gewijzigd.

   > [!NOTE]
   > Als de wacht woord-hash van de gebruiker is gesynchroniseerd met Azure AD met behulp van hash-synchronisatie van wacht woord, is er een kans dat het on-premises wachtwoord beleid zwakker is dan het beleid voor Cloud wachtwoord. In dit geval wordt het on-premises beleid afgedwongen. Dit beleid zorgt ervoor dat uw on-premises beleid wordt afgedwongen in de Cloud, ongeacht of u wachtwoord hash synchronisatie of Federatie gebruikt om eenmalige aanmelding te bieden.

1. Als de bewerking voor het instellen van een wacht woord mislukt, wordt de gebruiker door een fout gevraagd om het opnieuw te proberen. De bewerking kan niet worden uitgevoerd om de volgende redenen:
    * De service is niet actief.
    * Het wacht woord dat ze hebben geselecteerd, voldoet niet aan het beleid van de organisatie.
    * Kan de gebruiker niet vinden in de lokale AD DS omgeving.

   De fout berichten bevatten richt lijnen aan gebruikers, zodat ze kunnen proberen om op te lossen zonder tussen komst van de beheerder.

## <a name="password-writeback-security"></a>Wacht woord terugschrijven beveiliging

Het terugschrijven van wacht woorden is een zeer veilige service. Om ervoor te zorgen dat uw gegevens worden beveiligd, wordt een beveiligings model met vier lagen als volgt ingeschakeld:

* **Tenant-specifieke service-bus relay**
   * Wanneer u de service instelt, wordt een Tenant-specifieke service bus-relay ingesteld die wordt beveiligd door een wille keurig gegenereerd sterk wacht woord waarmee micro soft nooit toegang heeft.
* **Vergrendeld, cryptografisch sterk, versleutelings sleutel voor wacht woord**
   * Nadat de service bus relay is gemaakt, wordt er een sterke symmetrische sleutel gemaakt die wordt gebruikt voor het versleutelen van het wacht woord. Deze sleutel bevindt zich alleen in het geheime archief van uw bedrijf in de Cloud, wat sterk is vergrendeld en gecontroleerd, net als elk ander wacht woord in de Directory.
* **Industrie norm Transport Layer Security (TLS)**
   1. Wanneer een wacht woord opnieuw instellen of een wijzigings bewerking wordt uitgevoerd in de Cloud, wordt het Lees bare wacht woord versleuteld met uw open bare sleutel.
   1. Het versleutelde wacht woord wordt in een HTTPS-bericht geplaatst dat via een versleuteld kanaal wordt verzonden met behulp van micro soft TLS/SSL-certificaten naar uw service bus relay.
   1. Nadat het bericht in de service bus arriveert, wordt de on-premises agent geactiveerd en wordt de service bus geverifieerd met behulp van het sterke wacht woord dat eerder is gegenereerd.
   1. De on-premises agent haalt het versleutelde bericht op en ontsleutelt het met behulp van de persoonlijke sleutel.
   1. De on-premises agent probeert het wacht woord in te stellen via de AD DS SetPassword-API. Deze stap is wat het afdwingen van uw AD DS on-premises wachtwoord beleid (zoals de complexiteit, leeftijd, geschiedenis en filters) in de Cloud mogelijk maakt.
* **Verloop beleid voor berichten**
   * Als het bericht zich in service bus bevindt omdat uw on-premises service niet beschikbaar is, wordt er een time-out en na enkele minuten verwijderd. De time-out en het verwijderen van het bericht verhoogt de beveiliging nog verder.

### <a name="password-writeback-encryption-details"></a>Versleutelings gegevens voor wacht woord terugschrijven

Wanneer een gebruiker een wacht woord opnieuw instelt, wordt de aanvraag opnieuw ingesteld via verschillende versleutelings stappen voordat deze in uw on-premises omgeving arriveren. Deze versleutelings stappen garanderen maximale betrouw baarheid en beveiliging van de service. Deze worden als volgt beschreven:

1. **Wachtwoord versleuteling met 2048-bits RSA-sleutel**: wanneer een gebruiker een wacht woord verzendt om terug te schrijven naar on-premises, wordt het wacht woord zelf versleuteld met een 2048-bits RSA-sleutel.
1. **Versleuteling op pakket niveau met AES-GCM**: het hele pakket, het wacht woord plus de vereiste meta gegevens, wordt versleuteld met AES-GCM. Met deze versleuteling voor komt u dat iedereen met directe toegang tot het onderliggende Service Bus kanaal de inhoud kan weer geven of knoeien.
1. **Alle communicatie vindt plaats via TLS/SSL**: alle communicatie met Service bus gebeurt in een SSL/TLS-kanaal. Deze versleuteling beveiligt de inhoud van niet-gemachtigde externe partijen.
1. **Automatische rollover elke zes maanden**: alle sleutels worden elke zes maanden gekanteld, of telkens wanneer het terugschrijven van wacht woorden is uitgeschakeld en vervolgens weer wordt ingeschakeld op Azure AD Connect om te zorgen voor maximale service beveiliging en veiligheid.

### <a name="password-writeback-bandwidth-usage"></a>Gebruik van bandbreedte voor wacht woord terugschrijven

Wacht woord terugschrijven is een service met lage band breedte die alleen aanvragen naar de on-premises agent verzendt in de volgende omstandigheden:

* Er worden twee berichten verzonden wanneer de functie wordt in-of uitgeschakeld via Azure AD Connect.
* Eén bericht wordt om de vijf minuten als een service-heartbeat verzonden, zolang de service wordt uitgevoerd.
* Er worden twee berichten verzonden wanneer een nieuw wacht woord wordt verzonden:
   * Het eerste bericht is een aanvraag om de bewerking uit te voeren.
   * Het tweede bericht bevat het resultaat van de bewerking en wordt in de volgende gevallen verzonden:
      * Telkens wanneer een nieuw wacht woord wordt verzonden tijdens het opnieuw instellen van een wacht woord voor de selfservice voor gebruikers.
      * Telkens wanneer een nieuw wacht woord wordt verzonden tijdens een wijziging van het wacht woord voor gebruikers.
      * Telkens wanneer er een nieuw wacht woord wordt ingediend tijdens een door de beheerder geïnitieerde wacht woord opnieuw instellen (alleen van de Azure-beheer Portal).

#### <a name="message-size-and-bandwidth-considerations"></a>Overwegingen voor bericht grootte en-band breedte

De grootte van elk van de eerder beschreven berichten is doorgaans 1 KB. Zelfs onder extreme belastingen wordt de service voor het terugschrijven van wacht woorden zelf een aantal kilobits per seconde van band breedte verbruikt. Omdat elk bericht in realtime wordt verzonden, maar alleen wanneer dit wordt vereist door een wachtwoord update bewerking, en omdat de bericht grootte zo klein is, is het bandbreedte gebruik van de terugschrijf mogelijkheid te klein om een meet bare impact te hebben.

## <a name="supported-writeback-operations"></a>Ondersteunde back-upbewerkingen

Wacht woorden worden weer gegeven in de volgende situaties:

* **Ondersteunde bewerkingen voor eind gebruikers**
   * Een selfservice voor het wijzigen van een wacht woord voor een self-service voor eind gebruikers.
   * Wille keurige bewerking voor het wijzigen van een wacht woord voor de selfservice voor eind gebruikers, bijvoorbeeld wacht woord verloopt.
   * Een selfservice voor het opnieuw instellen van een wacht woord voor eind gebruikers die afkomstig is uit de portal voor het [opnieuw instellen van het wacht woord](https://passwordreset.microsoftonline.com).

* **Ondersteunde beheerders bewerkingen**
   * Een Administrator selfservice voor het wijzigen van het wacht woord.
   * Een door de beheerder selfservice bewerking voor het wijzigen van het wacht woord, bijvoorbeeld wacht woord verloopt.
   * Een selfservice voor wachtwoord herstel voor beheerders die afkomstig is uit de [Portal voor het opnieuw instellen](https://passwordreset.microsoftonline.com)van het wacht woord.
   * Een door de beheerder geïnitieerde wachtwoord herstel van de gebruiker van de [Azure Portal](https://portal.azure.com).
   * Een door de beheerder geïnitieerde wachtwoord herstel voor eind gebruikers van de [Microsoft Graph API bèta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta).

## <a name="unsupported-writeback-operations"></a>Niet-ondersteunde Write-bewerkingen

Wacht woorden worden niet weer gegeven in een van de volgende situaties:

* **Niet-ondersteunde bewerkingen voor eind gebruikers**
   * Eind gebruikers stellen hun eigen wacht woord opnieuw in met behulp van Power shell versie 1, versie 2 of de Microsoft Graph-API.
* **Niet-ondersteunde beheerders bewerkingen**
   * Een door de beheerder geïnitieerde wachtwoord herstel van de gebruiker wordt opnieuw ingesteld op basis van Power shell-versie 1, versie 2 of de Microsoft Graph-API (de [Microsoft Graph API Beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) wordt ondersteund).
   * Een door de beheerder geïnitieerde wachtwoord herstel voor eind gebruikers vanuit het [Microsoft 365-beheer centrum](https://admin.microsoft.com).
   * Een beheerder kan het hulp programma voor wachtwoord herstel niet gebruiken om hun eigen wacht woord opnieuw in te stellen voor wacht woord terugschrijven.

> [!WARNING]
> Gebruik van het selectie vakje ' gebruiker moet wacht woord bij volgende aanmelding wijzigen ' in on-premises AD DS beheer Programma's, zoals Active Directory gebruikers en computers, of de Active Directory-beheercentrum wordt ondersteund als een preview-functie van Azure AD Connect. Zie [wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende zelf studie om aan de slag te gaan met SSPR write-back:

> [!div class="nextstepaction"]
> [Zelf studie: write-back van self-service voor wacht woord opnieuw instellen (SSPR) inschakelen](./tutorial-enable-sspr-writeback.md)