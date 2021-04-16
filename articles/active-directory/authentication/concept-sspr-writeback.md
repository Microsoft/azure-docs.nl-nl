---
title: On-premises wachtwoord terugschrijven met selfservice voor wachtwoord opnieuw instellen - Azure Active Directory
description: Meer informatie over hoe gebeurtenissen voor wachtwoordwijziging of opnieuw instellen in Azure Active Directory kunnen worden teruggeschreven naar een on-premises directory-omgeving
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
ms.openlocfilehash: 4b8a84da331568d36b6f6910054fdb2aea76f490
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530323"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Hoe werkt write-back van self-service voor wachtwoord opnieuw instellen in Azure Active Directory?

met Azure Active Directory (Azure AD) selfservice voor wachtwoord opnieuw instellen (SSPR) kunnen gebruikers hun wachtwoord opnieuw instellen in de cloud, maar de meeste bedrijven hebben ook een on-premises Active Directory Domain Services-omgeving (AD DS) waarin hun gebruikers bestaan. Wachtwoord terugschrijven is een functie die is ingeschakeld met [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) waarmee wachtwoordwijzigingen in de cloud in realtime kunnen worden teruggeschreven naar een bestaande on-premises map. Wanneer gebruikers in deze configuratie hun wachtwoord wijzigen of opnieuw instellen met SSPR in de cloud, worden de bijgewerkte wachtwoorden ook teruggeschreven naar de on-premises AD DS omgeving

> [!IMPORTANT]
> In dit conceptuele artikel wordt aan een beheerder uitgelegd hoe write-back van selfservice voor wachtwoord opnieuw instellen werkt. Als u een eindgebruiker bent die al is geregistreerd voor self-service voor wachtwoordherstel en u weer toegang tot uw account wilt hebben, gaat u naar https://aka.ms/sspr.
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

Wachtwoord terugschrijven wordt ondersteund in omgevingen die gebruikmaken van de volgende hybride identiteitsmodellen:

* [Synchronisatie van wachtwoord-hashes](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Pass-through-verificatie](../hybrid/how-to-connect-pta.md)
* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Wachtwoord terugschrijven biedt de volgende functies:

* **Afdwinging van on-premises Active Directory Domain Services(AD DS)-wachtwoordbeleid:** wanneer een gebruiker zijn of haar wachtwoord opnieuw in stelt, wordt dit gecontroleerd om te controleren of het voldoet aan uw on-premises AD DS-beleid voordat deze wordt doorgezet naar die map. Deze beoordeling omvat het controleren van de geschiedenis, complexiteit, leeftijd, wachtwoordfilters en andere wachtwoordbeperkingen die u in de AD DS.
* **Feedback zonder vertraging:** Wachtwoord terugschrijven is een synchrone bewerking. Gebruikers worden onmiddellijk gewaarschuwd als hun wachtwoord niet aan het beleid voldoet of om welke reden dan ook niet opnieuw kan worden ingesteld of gewijzigd.
* Ondersteunt wachtwoordwijzigingen vanuit het toegangsvenster en **Microsoft 365:** wanneer gesynchroniseerde gebruikers met federatief of wachtwoordhashes hun verlopen of niet-verlopen wachtwoorden kunnen wijzigen, worden deze wachtwoorden teruggeschreven naar AD DS.
* Ondersteunt wachtwoord terugschrijven wanneer een beheerder deze opnieuw in de **Azure Portal** herstelt: wanneer een beheerder het wachtwoord van een gebruiker in de [Azure Portal opnieuw](https://portal.azure.com)in stelt, wordt het wachtwoord teruggeschreven naar on-premises als deze gebruiker federatief is of een wachtwoordhash is gesynchroniseerd. Deze functionaliteit wordt momenteel niet ondersteund in de Office-beheerportal.
* **Vereist geen inkomende firewallregels:** Voor het terugschrijven van wachtwoorden wordt een Azure Service Bus relay gebruikt als onderliggend communicatiekanaal. Alle communicatie is uitgaand via poort 443.

> [!NOTE]
> Beheerdersaccounts die binnen beveiligde groepen in on-premises AD bestaan, kunnen worden gebruikt met wachtwoord terugschrijven. Beheerders kunnen hun wachtwoord in de cloud wijzigen, maar kunnen het wachtwoord niet opnieuw instellen om een vergeten wachtwoord opnieuw in te stellen. Zie Beveiligde accounts en groepen in AD DS voor [meer informatie over beveiligde AD DS.](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory)

Voltooi de volgende zelfstudie om aan de slag te gaan met het terugschrijven van SSPR:

> [!div class="nextstepaction"]
> [Zelfstudie: Selfservice voor wachtwoord opnieuw instellen (SSPR) terugschrijven inschakelen](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Hoe wachtwoord terugschrijven werkt

Wanneer een federatief of met wachtwoordhash gesynchroniseerde gebruiker probeert het wachtwoord opnieuw in te stellen of te wijzigen in de cloud, worden de volgende acties uitgevoerd:

1. Er wordt een controle uitgevoerd om te zien welk type wachtwoord de gebruiker heeft. Als het wachtwoord on-premises wordt beheerd:
   * Er wordt een controle uitgevoerd om te zien of de write-back-service actief is. Als dat zo is, kan de gebruiker doorgaan.
   * Als de write-backservice niet werkt, wordt de gebruiker geïnformeerd dat het wachtwoord op dit moment niet opnieuw kan worden ingesteld.
1. Vervolgens geeft de gebruiker de juiste verificatiepoorten door en bereikt deze de **pagina Wachtwoord opnieuw** instellen.
1. De gebruiker selecteert een nieuw wachtwoord en bevestigt dit.
1. Wanneer de gebruiker Verzenden **selecteert,** wordt het wachtwoord zonder tekst versleuteld met een symmetrische sleutel die is gemaakt tijdens het installatieproces voor terugschrijven.
1. Het versleutelde wachtwoord is opgenomen in een nettolading die via een HTTPS-kanaal wordt verzonden naar uw tenantspecifieke Service Bus Relay (die voor u is ingesteld tijdens het installatieproces voor write-back). Deze relay wordt beveiligd door een willekeurig gegenereerd wachtwoord dat alleen uw on-premises installatie kent.
1. Nadat het bericht de servicebus heeft bereikt, wordt het eindpunt voor het opnieuw instellen van het wachtwoord automatisch weergegeven en ziet het dat er een aanvraag voor opnieuw instellen in behandeling is.
1. De service zoekt vervolgens naar de gebruiker met behulp van het kenmerk cloudanker. Om deze zoekactie te laten slagen, moet aan de volgende voorwaarden worden voldaan:

   * Het gebruikersobject moet bestaan in de AD DS connectorruimte.
   * Het gebruikersobject moet worden gekoppeld aan het bijbehorende metaverseobject (MV).
   * Het gebruikersobject moet worden gekoppeld aan het bijbehorende Azure AD-connectorobject.
   * De koppeling van het AD DS connectorobject naar de MV moet de synchronisatieregel `Microsoft.InfromADUserAccountEnabled.xxx` op de koppeling hebben.

   Wanneer de aanroep afkomstig is van de cloud, gebruikt de synchronisatie-engine het kenmerk **cloudAnchor** om het ruimteobject van de Azure AD-connector op te zoeken. Vervolgens wordt de koppeling naar het MV-object volgt en vervolgens de koppeling terug naar het AD DS object. Omdat er meerdere AD DS (meerdere forest) voor dezelfde gebruiker kunnen zijn, is de synchronisatie-engine afhankelijk van de koppeling om de juiste `Microsoft.InfromADUserAccountEnabled.xxx` te kiezen.

1. Nadat het gebruikersaccount is gevonden, wordt geprobeerd het wachtwoord rechtstreeks in het juiste forest opnieuw in AD DS stellen.
1. Als de bewerking voor het instellen van het wachtwoord is geslaagd, krijgt de gebruiker te horen dat het wachtwoord is gewijzigd.

   > [!NOTE]
   > Als de wachtwoordhash van de gebruiker wordt gesynchroniseerd met Azure AD met behulp van wachtwoord-hashsynchronisatie, bestaat de kans dat het on-premises wachtwoordbeleid zwakker is dan het wachtwoordbeleid voor de cloud. In dit geval wordt het on-premises beleid afgedwongen. Dit beleid zorgt ervoor dat uw on-premises beleid wordt afgedwongen in de cloud, ongeacht of u wachtwoordhashsynchronisatie of federatie gebruikt om een aantal aanmeldingen te bieden.

1. Als de bewerking voor het instellen van het wachtwoord mislukt, wordt de gebruiker gevraagd het opnieuw te proberen. De bewerking kan om de volgende redenen mislukken:
    * De service was niet in gebruik.
    * Het wachtwoord dat ze hebben geselecteerd, voldoet niet aan het beleid van de organisatie.
    * Kan de gebruiker niet vinden in de lokale AD DS omgeving.

   De foutberichten bieden richtlijnen aan gebruikers, zodat ze kunnen proberen op te lossen zonder tussenkomst van de beheerder.

## <a name="password-writeback-security"></a>Beveiliging voor wachtwoord terugschrijven

Wachtwoord terugschrijven is een uiterst veilige service. Om ervoor te zorgen dat uw gegevens zijn beveiligd, wordt een beveiligingsmodel met vier lagen als volgt ingeschakeld:

* **Tenantspecifieke Service Bus Relay**
   * Bij het instellen van de service wordt een tenantspecifieke Service Bus Relay ingesteld die wordt beveiligd door een willekeurig gegenereerd sterk wachtwoord waar Microsoft nooit toegang toe heeft.
* **Vergrendeld, cryptografisch sterk, wachtwoordversleutelingssleutel**
   * Nadat de Service Bus Relay is gemaakt, wordt er een sterke symmetrische sleutel gemaakt die wordt gebruikt om het wachtwoord te versleutelen wanneer het via de kabel wordt verzonden. Deze sleutel staat alleen in het geheime opslag van uw bedrijf in de cloud, die sterk is vergrendeld en gecontroleerd, net als elk ander wachtwoord in de directory.
* **Industriestandaard Transport Layer Security (TLS)**
   1. Wanneer een wachtwoord opnieuw wordt ingesteld of gewijzigd in de cloud, wordt het niet-versleutelde wachtwoord versleuteld met uw openbare sleutel.
   1. Het versleutelde wachtwoord wordt in een HTTPS-bericht geplaatst dat via een versleuteld kanaal wordt verzonden met behulp van Microsoft TLS/SSL-certificaten naar uw Service Bus Relay.
   1. Nadat het bericht in de servicebus is aangekomen, wordt uw on-premises agent bij de servicebus geauthenticeerd met behulp van het sterke wachtwoord dat eerder is gegenereerd.
   1. De on-premises agent haalt het versleutelde bericht op en ontsleutelt het met behulp van de persoonlijke sleutel.
   1. De on-premises agent probeert het wachtwoord in te stellen via de AD DS SetPassword-API. Met deze stap kunt u uw AD DS on-premises wachtwoordbeleid afdwingen (zoals de complexiteit, leeftijd, geschiedenis en filters) in de cloud.
* **Beleid voor verlopen berichten**
   * Als het bericht zich in de Service Bus bevindt omdat uw on-premises service niet werkt, tlijnt het bericht en wordt het na enkele minuten verwijderd. De time-out en verwijdering van het bericht verhoogt de beveiliging nog verder.

### <a name="password-writeback-encryption-details"></a>Versleutelingsdetails voor wachtwoord terugschrijven

Nadat een gebruiker een wachtwoord opnieuw heeft ingesteld, doormaakt de aanvraag voor opnieuw instellen verschillende versleutelingsstappen voordat deze in uw on-premises omgeving aankomt. Deze versleutelingsstappen zorgen voor maximale betrouwbaarheid en beveiliging van de service. Deze worden als volgt beschreven:

1. **Wachtwoordversleuteling met 2048-bits RSA-sleutel:** nadat een gebruiker een wachtwoord heeft verzonden dat moet worden teruggeschreven naar on-premises, wordt het ingediende wachtwoord zelf versleuteld met een 2048-bits RSA-sleutel.
1. **Versleuteling op pakketniveau met AES-GCM:** het hele pakket, het wachtwoord plus de vereiste metagegevens, wordt versleuteld met AES-GCM. Deze versleuteling voorkomt dat iedereen met directe toegang tot het onderliggende Service Bus kanaal de inhoud kan weergeven of knoeien met de inhoud.
1. **Alle communicatie vindt plaats via TLS/SSL:** alle communicatie met Service Bus gebeurt in een SSL/TLS-kanaal. Deze versleuteling beveiligt de inhoud van niet-geautoriseerde derden.
1. **Automatische** sleuteloverboeking om de zes maanden: alle sleutels worden elke zes maanden overgeslagen of elke keer dat wachtwoord terugschrijven wordt uitgeschakeld en vervolgens opnieuw wordt ingeschakeld op Azure AD Connect, om een maximale beveiliging en veiligheid van de service te garanderen.

### <a name="password-writeback-bandwidth-usage"></a>Bandbreedtegebruik voor wachtwoord terugschrijven

Wachtwoord terugschrijven is een service met lage bandbreedte die onder de volgende omstandigheden alleen aanvragen naar de on-premises agent verzendt:

* Er worden twee berichten verzonden wanneer de functie is ingeschakeld of uitgeschakeld via Azure AD Connect.
* Er wordt één bericht om de vijf minuten verzonden als een service-heartbeat zolang de service wordt uitgevoerd.
* Telkens als er een nieuw wachtwoord wordt verzonden, worden er twee berichten verzonden:
   * Het eerste bericht is een aanvraag om de bewerking uit te voeren.
   * Het tweede bericht bevat het resultaat van de bewerking en wordt in de volgende omstandigheden verzonden:
      * Telkens als er een nieuw wachtwoord wordt verzonden tijdens de selfservice voor wachtwoord opnieuw instellen van een gebruiker.
      * Telkens als er een nieuw wachtwoord wordt verzonden tijdens een bewerking voor het wijzigen van het gebruikerswachtwoord.
      * Telkens als er een nieuw wachtwoord wordt verzonden tijdens een door de beheerder geïnitieerde gebruikerswachtwoord opnieuw instellen (alleen vanuit de Azure-beheerportals).

#### <a name="message-size-and-bandwidth-considerations"></a>Overwegingen voor berichtgrootte en bandbreedte

De grootte van elk van de eerder beschreven berichten is doorgaans minder dan 1 kB. Zelfs bij extreme belastingen verbruikt de service voor het terugschrijven van wachtwoorden zelf een paar kilobits per seconde bandbreedte. Omdat elk bericht in realtime wordt verzonden, alleen wanneer dit is vereist door een wachtwoordupdatebewerking en omdat de berichtgrootte zo klein is, is het bandbreedtegebruik van de mogelijkheid voor terugschrijven te klein om meetbare gevolgen te hebben.

## <a name="supported-writeback-operations"></a>Ondersteunde write-back-bewerkingen

Wachtwoorden worden teruggeschreven in de volgende situaties:

* **Ondersteunde bewerkingen voor eindgebruikers**
   * Elke selfservice voor het wijzigen van het wachtwoord door eindgebruikers.
   * Elke selfservice voor eindgebruikers forceer de bewerking voor het wijzigen van het wachtwoord, bijvoorbeeld het verlopen van wachtwoorden.
   * Selfservice voor wachtwoord opnieuw instellen voor eindgebruikers die afkomstig zijn van de portal voor [het opnieuw instellen van wachtwoorden.](https://passwordreset.microsoftonline.com)

* **Ondersteunde beheerdersbewerkingen**
   * Een beheerder die vrijwillig een wachtwoord wijzigt.
   * Elke selfservicebeheerder forceer de bewerking voor het wijzigen van het wachtwoord, bijvoorbeeld het verlopen van wachtwoorden.
   * Selfservice voor wachtwoord opnieuw instellen voor beheerders die afkomstig zijn van de [portal voor het opnieuw instellen van wachtwoorden.](https://passwordreset.microsoftonline.com)
   * Door de beheerder geïnitieerde wachtwoord opnieuw instellen van de Azure Portal [.](https://portal.azure.com)
   * Door de beheerder geïnitieerde wachtwoord opnieuw instellen van de Microsoft Graph [API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http).

## <a name="unsupported-writeback-operations"></a>Niet-ondersteunde terugschrijvenbewerkingen

Wachtwoorden worden niet teruggeschreven in een van de volgende situaties:

* **Niet-ondersteunde bewerkingen voor eindgebruikers**
   * Elke eindgebruiker moet zijn eigen wachtwoord opnieuw instellen met behulp van PowerShell versie 1, versie 2 of de Microsoft Graph API.
* **Niet-ondersteunde beheerdersbewerkingen**
   * Door een beheerder geïnitieerde wachtwoord opnieuw instellen vanuit PowerShell versie 1, versie 2 of de Microsoft Graph-API (de [Microsoft Graph-API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http) wordt ondersteund).
   * Door de beheerder geïnitieerde wachtwoord opnieuw instellen vanuit de [Microsoft 365-beheercentrum](https://admin.microsoft.com).
   * Beheerders kunnen het hulpprogramma voor wachtwoord opnieuw instellen niet gebruiken om hun eigen wachtwoord opnieuw in te stellen voor het terugschrijven van wachtwoorden.

> [!WARNING]
> Gebruik van het selectievakje 'Gebruiker moet wachtwoord wijzigen bij volgende aanmelding' in on-premises AD DS-beheerprogramma's zoals Active Directory: gebruikers en computers of de Active Directory-beheercentrum wordt ondersteund als preview-functie van Azure AD Connect. Zie Synchronisatie van [wachtwoordhashs implementeren met Azure AD Connect synchronisatie voor meer informatie.](../hybrid/how-to-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende zelfstudie om aan de slag te gaan met SSPR-terugschrijven:

> [!div class="nextstepaction"]
> [Zelfstudie: Selfservice voor wachtwoord opnieuw instellen (SSPR) terugschrijven inschakelen](./tutorial-enable-sspr-writeback.md)