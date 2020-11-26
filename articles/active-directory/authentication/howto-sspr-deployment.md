---
title: Overwegingen voor de implementatie van Azure Active Directory self-service voor wachtwoord herstel
description: Meer informatie over overwegingen met betrekking tot de implementatie en strategie voor een succes volle implementatie van de selfservice voor wachtwoord herstel van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 765bfe0f805ae4219110d689e8f7f8fd54a26cf3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173933"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Een Azure Active Directory self-service voor het opnieuw instellen van wacht woorden plannen

> [!IMPORTANT]
> Dit implementatie plan bevat richt lijnen en aanbevolen procedures voor het implementeren van selfservice voor wachtwoord herstel (SSPR) van Azure AD.
>
> **Als u een eind gebruiker bent en wilt terugkeren naar uw account, gaat [https://aka.ms/sspr](https://aka.ms/sspr)** u naar.

[Self-service voor wachtwoord herstel (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) is een functie van Azure Active Directory (AD) waarmee gebruikers hun wacht woord opnieuw kunnen instellen zonder contact op te nemen met IT-mede werkers. De gebruikers kunnen een snelle blok kering opheffen en blijven werken, ongeacht waar ze zijn of het tijdstip van de dag. Door de mede werkers in staat te stellen zichzelf te deblokkeren, kan uw organisatie de niet-productieve tijd en hoge ondersteunings kosten voor de meest voorkomende problemen met betrekking tot het wacht woord verlagen.

SSPR heeft de volgende belang rijke mogelijkheden:

* Met self-service kunnen eind gebruikers hun verlopen of niet-verlopen wacht woorden opnieuw instellen zonder contact op te nemen met een beheerder of Help Desk voor ondersteuning.
* Met [wacht woord terugschrijven](./concept-sspr-writeback.md) kunt u on-premises wacht woorden en omzetting van account vergrendeling als de Cloud beheren.
* Met activiteiten rapporten voor wachtwoord beheer kunt u beheerders inzicht geven in het opnieuw instellen van wacht woorden en registratie activiteiten in hun organisatie.

In deze implementatie handleiding wordt uitgelegd hoe u een SSPR implementeert en test.

Als u snel SSPR in actie wilt zien en vervolgens meer wilt weten over aanvullende overwegingen bij de implementatie:

> [!div class="nextstepaction"]
> [Selfservice voor wachtwoord herstel inschakelen (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Meer informatie over SSPR

Meer informatie over SSPR. Zie [hoe het werkt: Azure AD selfservice voor wacht woord opnieuw instellen](./concept-sspr-howitworks.md).

### <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voor delen van het inschakelen van SSPR zijn:

* **Kosten beheren**. SSPR reduceert IT-ondersteunings kosten door gebruikers in staat te stellen hun wacht woorden op hun eigen manier te herstellen. Het vermindert ook de tijd die verloren gaat vanwege verloren wacht woorden en vergren delingen. 

* **Intuïtieve gebruikers ervaring**. Het biedt een intuïtief proces voor eenmalige gebruikers registratie waarmee gebruikers hun wacht woorden opnieuw kunnen instellen en accounts op aanvraag van elk apparaat of elke locatie kunt deblokkeren. SSPR stelt gebruikers in staat om sneller te werken en productiever te zijn.

* **Flexibiliteit en beveiliging**. SSPR maakt ondernemingen toegang tot de beveiliging en flexibiliteit die een Cloud platform biedt. Beheerders kunnen instellingen wijzigen om nieuwe beveiligings vereisten toe te passen en deze wijzigingen door te laten draaien aan gebruikers zonder hun aanmelding te onderbreken.

* **Robuuste controle en bijhouden** van het gebruik. Een organisatie kan ervoor zorgen dat de bedrijfs systemen veilig blijven terwijl de gebruikers hun eigen wacht woorden opnieuw instellen. Robuuste audit logboeken bevatten informatie over elke stap van het proces voor het opnieuw instellen van het wacht woord. Deze logboeken zijn beschikbaar via een API en kunnen de gebruiker de gegevens importeren in een SIEM-systeem (Security Incident and Event monitoring).

### <a name="licensing"></a>Licentieverlening

Azure Active Directory wordt per gebruiker in licentie gegeven, wat betekent dat elke gebruiker een geschikte licentie nodig heeft voor de functies die ze gebruiken. U wordt aangeraden om op groep gebaseerde licentie verlening voor SSPR te verlenen. 

Zie [licentie vereisten voor Azure AD selfservice voor wacht woord opnieuw instellen](./concept-sspr-licensing.md)voor het vergelijken van edities en functies en het inschakelen van licentie verlening op basis van groepen of gebruikers.

Zie [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over prijzen.

### <a name="prerequisites"></a>Vereisten

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld. [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) indien nodig.

* Een account met de bevoegdheden van een globale beheerder.


### <a name="training-resources"></a>Trainings bronnen

| Resources| Koppeling en beschrijving |
| - | - |
| Video's| [Bied uw gebruikers betere IT-schaal baarheid](https://youtu.be/g9RpRnylxS8) 
| |[Wat is self-service voor wachtwoordherstel?](https://youtu.be/hc97Yx5PJiM)|
| |[Self-service voor wachtwoord herstel implementeren](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Hoe kan ik de selfservice voor wachtwoord herstel voor gebruikers configureren in azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[[Gebruikers voorbereiden om] de beveiligings informatie voor Azure Active Directory te registreren](https://youtu.be/gXuh0XS18wA) |
| Online cursussen|[Identiteiten beheren in Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Gebruik SSPR om uw gebruikers een moderne, beveiligde ervaring te geven. Zie met name de module '[beheer van Azure Active Directory-gebruikers en-groepen](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)'. |
|Betaalde cursussen Pluralsight |[De problemen met identiteits-en toegangs beheer](https://www.pluralsight.com/courses/identity-access-management-issues) Meer informatie over IAM-en beveiligings problemen waarvan u rekening moet houden in uw organisatie. Zie vooral de module ' andere verificatie methoden '.|
| |[Aan de slag met de micro soft Enter prise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Meer informatie over de aanbevolen procedures voor het uitbreiden van on-premises assets naar de Cloud op een manier die verificatie, autorisatie, versleuteling en een beveiligde mobiele ervaring mogelijk maakt. Zie met name de module ' geavanceerde functies van Microsoft Azure Active Directory Premium configureren '.
|Zelfstudies |[Een pilot van een Azure AD-selfservice voor wachtwoordherstel uitrollen](./tutorial-enable-sspr.md) |
| |[Wachtwoord terugschrijven inschakelen](./tutorial-enable-sspr-writeback.md) |
| |[Azure AD-wacht woord opnieuw instellen vanuit het aanmeldings scherm voor Windows 10](./howto-sspr-windows.md) |
| Veelgestelde vragen|[Veelgestelde vragen over wachtwoord beheer](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>Architectuur voor de oplossing

In het volgende voor beeld wordt de architectuur voor het opnieuw instellen van wacht woorden voor algemene hybride omgevingen beschreven.

![diagram van de oplossings architectuur](./media/howto-sspr-deployment//solutions-architecture.png)

Beschrijving van werk stroom

Om het wacht woord opnieuw in te stellen, gaan gebruikers naar de portal voor het [opnieuw instellen](https://aka.ms/sspr)van het wacht woord. Ze moeten de eerder geregistreerde verificatie methode of-methoden controleren om hun identiteit te bewijzen. Als het wacht woord opnieuw is ingesteld, beginnen ze met het reset proces.

* Voor Cloud gebruikers slaat SSPR het nieuwe wacht woord op in azure AD. 

* Voor hybride gebruikers schrijft SSPR het wacht woord terug naar de on-premises Active Directory via de Azure AD Connect-service. 

Opmerking: voor gebruikers met een [wacht woord-hash-synchronisatie (PHS)](../hybrid/whatis-phs.md) is uitgeschakeld, slaat SSPR de wacht woorden alleen op in de on-premises Active Directory.

### <a name="best-practices"></a>Aanbevolen procedures

U kunt gebruikers helpen om snel te registreren door SSPR te implementeren naast een andere populaire toepassing of service in de organisatie. Met deze actie wordt een groot aantal aanmeldingen gegenereerd en wordt de registratie van het apparaat uitgevoerd.

Voordat u SSPR implementeert, kunt u ervoor kiezen om het aantal en de gemiddelde kosten van elke aanroep voor het opnieuw instellen van een wacht woord te bepalen. U kunt deze gegevens na de implementatie gebruiken om weer te geven dat de waarde SSPR naar de organisatie gaat.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Gecombineerde registratie inschakelen voor SSPR en MFA

Micro soft raadt organisaties aan de gecombineerde registratie-ervaring voor SSPR en multi-factor Authentication in te scha kelen. Wanneer u deze gecombineerde registratie-ervaring inschakelt, hoeven gebruikers slechts eenmaal hun registratie gegevens te selecteren om beide functies in te scha kelen.

De gecombineerde registratie-ervaring vereist niet dat organisaties zowel de SSPR als de Azure AD-Multi-Factor Authentication inschakelen. Gecombineerde registratie biedt organisaties een betere gebruikers ervaring. Zie voor meer informatie [registratie van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>Het implementatie project plannen

Houd rekening met de behoeften van uw organisatie terwijl u de strategie voor deze implementatie in uw omgeving bepaalt.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden](../fundamentals/active-directory-deployment-plans.md) en de rol van belanghebbenden in het project goed begrijpen door de belanghebbenden en hun project invoer en accountabilities te documenteren.

#### <a name="required-administrator-roles"></a>Vereiste beheerders rollen


| Zakelijke rol/Persoona| Azure AD-rol (indien nodig) |
| - | - |
| Help Desk van niveau 1| Wachtwoordbeheerder |
| Help Desk van niveau 2| Gebruikersbeheerder |
| SSPR-beheerder| Globale beheerder |


### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. U moet proactief communiceren met uw gebruikers hoe hun ervaring verandert, wanneer deze wordt gewijzigd, en hoe u ondersteuning krijgt als u problemen ondervindt. Lees de [self-service voor het opnieuw instellen van wacht woorden op het micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=56768) voor ideeën over het plannen van de communicatie strategie van uw eind gebruikers.

### <a name="plan-a-pilot"></a>Een pilot plannen

We raden u aan de initiële configuratie van SSPR in een test omgeving te hebben. Begin met een test groep door SSPR in te scha kelen voor een subset van gebruikers in uw organisatie. Bekijk [Aanbevolen procedures voor een pilot](../fundamentals/active-directory-deployment-plans.md).

Zie [een groep maken en leden toevoegen in azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)voor het maken van een groep. 

## <a name="plan-configuration"></a>Configuratie plannen

De volgende instellingen zijn vereist om SSPR samen met aanbevolen waarden in te scha kelen.

| Onderwerp | Instelling | Waarde |
| --- | --- | --- |
| **SSPR-eigenschappen** | Self-service voor wacht woord opnieuw instellen is ingeschakeld | **Geselecteerde** groep voor pilot/ **all** voor productie |
| **Verificatiemethoden** | Verificatie methoden die nodig zijn om te registreren | Altijd 1 meer dan vereist voor opnieuw instellen |
|   | Verificatie methoden die nodig zijn om opnieuw in te stellen | Een of twee |
| **Registratie** | Vereisen dat gebruiker zich bij aanmelding registreren | Yes |
|   | Het aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen | 90 – 180 dagen |
| **Meldingen** | Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord | Yes |
|   | Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen | Yes |
| **Aanpassing** | Help Desk-koppeling aanpassen | Yes |
|   | Aangepaste e-mail adres of URL voor de Help Desk | Ondersteunings site of e-mail adres |
| **On-premises integratie** | Wacht woorden terugschrijven naar on-premises AD | Yes |
|   | Gebruikers toestaan om het account te ontgrendelen zonder het wacht woord opnieuw in te stellen | Yes |

### <a name="sspr-properties"></a>SSPR-eigenschappen

Wanneer u SSPR inschakelt, kiest u een geschikte beveiligings groep in de test omgeving.

* Als u SSPR registratie voor iedereen wilt afdwingen, raden we u aan de optie **all** te gebruiken.
* Anders selecteert u de juiste Azure AD-of AD-beveiligings groep.

### <a name="authentication-methods"></a>Verificatiemethoden

Wanneer SSPR is ingeschakeld, kunnen gebruikers hun wacht woord alleen opnieuw instellen als ze gegevens bevatten in de verificatie methoden die de beheerder heeft ingeschakeld. Methoden zijn onder andere telefoon, verificator-app-melding, beveiligings vragen, enzovoort. Zie [Wat zijn verificatie methoden?](./concept-authentication-methods.md)voor meer informatie.

We raden u aan de volgende verificatie methode-instellingen te configureren:

* Stel de **vereiste authenticatie methoden in voor registratie** bij ten minste één waarde voor het aantal dat moet worden ingesteld. Het toestaan van meerdere verificaties biedt gebruikers flexibiliteit wanneer ze opnieuw moeten worden ingesteld.

* Stel het **aantal methoden in** dat moet worden ingesteld op een niveau dat geschikt is voor uw organisatie. Voor één is de minste wrijving vereist, terwijl twee de beveiligings postuur kan verg Roten. 

Opmerking: de gebruiker moet de verificatie methoden hebben geconfigureerd in het [wachtwoord beleid en de beperkingen in azure Active Directory](./concept-sspr-policy.md).

### <a name="registration-settings"></a>Registratie-instellingen

Stel **in dat gebruikers zich moeten registreren wanneer ze zich aanmelden** bij **Ja**. Als u deze instelling inschakelt, moeten gebruikers zich registreren bij het aanmelden om ervoor te zorgen dat alle gebruikers worden beveiligd.

Stel het **aantal dagen in waarna gebruikers wordt gevraagd om hun verificatie gegevens te bevestigen** tot een waarde tussen **90** en **180** dagen, tenzij uw organisatie voor een kortere periode een bedrijfs behoefte heeft.

### <a name="notifications-settings"></a>Instellingen voor meldingen

Configureer de **gebruikers op de hoogte van het opnieuw instellen van wacht woorden** en stel **alle beheerders op de hoogte wanneer andere beheerders hun wacht woord opnieuw instellen** op **Ja**. Als u **Ja** selecteert, wordt de beveiliging verhoogd door ervoor te zorgen dat gebruikers op de hoogte zijn wanneer hun wacht woord opnieuw wordt ingesteld. Het zorgt er ook voor dat alle beheerders op de hoogte zijn wanneer een beheerder een wacht woord wijzigt. Als gebruikers of beheerders een melding ontvangen en ze de wijziging niet hebben gestart, kunnen ze onmiddellijk een mogelijk beveiligings probleem melden.

### <a name="customization-settings"></a>Aanpassings instellingen

Het is essentieel om de e-mail adres of URL van de Help Desk aan te passen om ervoor te zorgen dat gebruikers die problemen ervaren, direct hulp kunnen krijgen. Stel deze optie in op een gemeen schappelijke e-mail adres van de Help Desk of de webpagina waarmee uw gebruikers bekend zijn. 

Zie [de Azure AD-functionaliteit aanpassen voor selfservice voor wachtwoord herstel](./howto-sspr-customization.md)voor meer informatie.

### <a name="password-writeback"></a>Wacht woord terugschrijven

**Wacht woord terugschrijven** is ingeschakeld met [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) en schrijft wacht woorden opnieuw in de Cloud terug naar een bestaande on-premises Directory in real time. Zie [Wat is wacht woord terugschrijven?](./concept-sspr-writeback.md) voor meer informatie.

We raden u aan de volgende instellingen te volgen:

* Zorg ervoor dat het **terugschrijven van wacht woorden naar on-premises AD** is ingesteld op **Ja**. 
* Stel het **selectie beleid toestaan dat gebruikers het account ontgrendelen zonder wacht woord opnieuw** in op **Ja**.

Standaard worden accounts door Azure AD ontgrendeld wanneer het wacht woord opnieuw wordt ingesteld.

### <a name="administrator-password-setting"></a>Instelling voor beheerders wachtwoord

Beheerders accounts hebben verhoogde machtigingen. De on-premises onderneming of domein beheerders kunnen hun wacht woord niet opnieuw instellen via SSPR. On-premises beheerders accounts hebben de volgende beperkingen:

* kan het wacht woord alleen wijzigen in hun on-premises omgeving.
* kan nooit de geheime vragen en antwoorden gebruiken als methode om het wacht woord opnieuw in te stellen.

U wordt aangeraden om uw on-premises Active Directory-beheerders accounts niet te synchroniseren met Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Omgevingen met meerdere systemen voor identiteits beheer

Sommige omgevingen hebben meerdere systemen voor identiteits beheer. On-premises identiteits managers als Oracle AM en SiteMinder vereisen synchronisatie met AD voor wacht woorden. U kunt dit doen met behulp van een hulp programma zoals de meldings service voor wachtwoord wijzigingen (PCNS) met Microsoft Identity Manager (MIM). Zie het artikel [de MIM-meldings service voor wachtwoord wijzigingen implementeren op een domein controller](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)voor meer informatie over dit complexe scenario.

## <a name="plan-testing-and-support"></a>Testen en ondersteuning plannen

Zorg ervoor dat in elke fase van de implementatie van de eerste test groepen via de hele organisatie de resultaten worden verwacht.

### <a name="plan-testing"></a>Tests plannen

Om ervoor te zorgen dat uw implementatie werkt zoals verwacht, moet u een set test cases plannen om de implementatie te valideren. Als u de test cases wilt beoordelen, moet u een gebruiker die geen beheerder is, een wacht woord hebben. Als u een gebruiker wilt maken, raadpleegt u [nieuwe gebruikers toevoegen aan Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

De volgende tabel bevat nuttige test scenario's die u kunt gebruiken om uw organisaties de verwachte resultaten te documenteren op basis van uw beleid.
<br>


| Business Case| Verwachte resultaten |
| - | - |
| SSPR Portal is toegankelijk vanuit het bedrijfs netwerk| Bepaald door uw organisatie |
| SSPR Portal is toegankelijk vanaf buiten het bedrijfs netwerk| Bepaald door uw organisatie |
| Het gebruikers wachtwoord opnieuw instellen vanuit de browser wanneer de gebruiker niet is ingeschakeld voor het opnieuw instellen van het wacht woord| De gebruiker heeft geen toegang tot de stroom voor het opnieuw instellen van het wacht woord |
| Het gebruikers wachtwoord opnieuw instellen vanuit de browser wanneer de gebruiker niet is geregistreerd voor het opnieuw instellen van het wacht woord| De gebruiker heeft geen toegang tot de stroom voor het opnieuw instellen van het wacht woord |
| Gebruiker meldt zich aan wanneer wordt afgedwongen voor registratie van wacht woord opnieuw instellen| De gebruiker wordt gevraagd om beveiligings gegevens te registreren |
| Gebruiker meldt zich aan als registratie van wacht woord opnieuw instellen is voltooid| De gebruiker wordt gevraagd om beveiligings gegevens te registreren |
| De SSPR-Portal is toegankelijk als de gebruiker geen licentie heeft| Is toegankelijk |
| Het gebruikers wachtwoord opnieuw instellen vanaf het vergrendelings scherm van Windows 10 Azure AD dat is toegevoegd aan het gekoppelde of hybride Azure AD-apparaat| Gebruiker kan wacht woord opnieuw instellen |
| SSPR-registratie en gebruiks gegevens zijn bijna in real time beschikbaar voor beheerders| Is beschikbaar via audit logboeken |

U kunt ook verwijzen naar [een Azure AD self-service voor het opnieuw instellen van wacht woorden](./tutorial-enable-sspr.md). In deze zelf studie maakt u een pilot-implementatie van SSPR in uw organisatie mogelijk en test u een niet-beheerders account.

### <a name="plan-support"></a>Ondersteuning plannen

Hoewel SSPR doorgaans geen gebruikers problemen maakt, is het belang rijk om ondersteunings medewerkers voor te bereiden op problemen die zich kunnen voordoen. Hoewel een beheerder het wacht woord voor eind gebruikers via de Azure AD-Portal opnieuw kan instellen, is het beter om het probleem op te lossen via een self-service-ondersteunings proces.

Om het succes van uw ondersteunings team in te scha kelen, kunt u een veelgestelde vragen maken op basis van vragen die u van uw gebruikers ontvangt. Enkele voorbeelden:

| Scenario's| Description |
| - | - |
| De gebruiker heeft geen geregistreerde verificatie methoden beschikbaar| Een gebruiker probeert het wacht woord opnieuw in te stellen, maar heeft geen van de authenticatie methoden die ze beschikbaar hebben (voor beeld: ze hebben hun mobiele telefoon thuis verlaten en kunnen geen toegang krijgen tot e-mail) |
| De gebruiker ontvangt geen tekst of gesprek op hun kantoor of mobiele telefoon| Een gebruiker probeert zijn identiteit te verifiëren via tekst of gesprek, maar ontvangt geen tekst/oproep. |
| Gebruiker heeft geen toegang tot de portal voor het opnieuw instellen van wacht woorden| Een gebruiker wil het wacht woord opnieuw instellen, maar is niet ingeschakeld voor wachtwoord herstel en heeft geen toegang tot de pagina om wacht woorden bij te werken. |
| Gebruiker kan geen nieuw wacht woord instellen| Een gebruiker heeft de verificatie voltooid tijdens de stroom voor het opnieuw instellen van het wacht woord, maar kan geen nieuw wacht woord instellen. |
| De gebruiker ziet een koppeling voor het opnieuw instellen van het wacht woord op een Windows 10-apparaat niet| Een gebruiker probeert het wacht woord opnieuw in te stellen vanaf het vergrendelings scherm van Windows 10, maar het apparaat is niet gekoppeld aan Azure AD of het intune-apparaatbeleid is niet ingeschakeld |

### <a name="plan-rollback"></a>Plan terugdraai actie

De implementatie ongedaan maken:

* voor één gebruiker verwijdert u de gebruiker uit de beveiligings groep 

* voor een groep verwijdert u de groep uit de SSPR-configuratie

* Schakel voor iedereen SSPR uit voor de Azure AD-Tenant

## <a name="deploy-sspr"></a>SSPR implementeren

Voordat u implementeert, moet u ervoor zorgen dat u het volgende hebt gedaan:

1. Gemaakt en gestart met het uitvoeren van uw [communicatie plan](#plan-communications).

1. De juiste [configuratie-instellingen](#plan-configuration)bepaald.

1. Identificeert de gebruikers en groepen voor de [test](#plan-a-pilot) -en productie omgevingen.

1. [Bepaalde configuratie-instellingen](#plan-configuration) voor registratie en self-service.

1. [Geconfigureerde wacht woord terugschrijven](#password-writeback) als u een hybride omgeving hebt.


**U bent nu klaar om SSPR te implementeren.**

Zie [selfservice voor wacht woord opnieuw instellen inschakelen](./tutorial-enable-sspr.md#enable-self-service-password-reset) voor volledige stapsgewijze instructies voor het configureren van de volgende gebieden.

1. [Verificatiemethoden](./concept-authentication-methods.md)

1. [Registratie-instellingen](./concept-registration-mfa-sspr-combined.md)

1. [Instellingen voor meldingen](#notifications-settings)

1. [Aanpassings instellingen](./howto-sspr-customization.md)

1. [On-premises integratie](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>SSPR in Windows inschakelen
Voor computers met Windows 7, 8, 8,1 en 10 kunt u [gebruikers in staat stellen hun wacht woord opnieuw in te stellen in het Windows-aanmeldings scherm](./howto-sspr-windows.md)

## <a name="manage-sspr"></a>SSPR beheren

Azure AD kan extra informatie over uw SSPR-prestaties bieden via audits en rapporten.

### <a name="password-management-activity-reports"></a>Rapporten voor wachtwoord beheer activiteiten 

U kunt vooraf gemaakte rapporten op Azure Portal gebruiken om de prestaties van de SSPR te meten. Als u een juiste licentie hebt, kunt u ook aangepaste query's maken. Zie [rapportage opties voor Azure AD-wachtwoord beheer](./howto-sspr-reporting.md) voor meer informatie.

> [!NOTE]
>  U moet [een globale beheerder](../roles/permissions-reference.md)zijn en u moet zich aanmelden om deze gegevens te verzamelen voor uw organisatie. Als u zich wilt aanmelden, moet u ten minste één keer naar het tabblad rapportage of de audit Logboeken in azure Portal. Tot die toe worden de gegevens niet verzameld voor uw organisatie.

Audit logboeken voor registratie en het opnieuw instellen van wacht woorden zijn 30 dagen beschikbaar. Als de beveiligings controle binnen uw bedrijf een langere retentie vereist, moeten de logboeken worden geëxporteerd en gebruikt in een SIEM-hulp programma zoals [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk of ArcSight.

![Scherm opname van SSPR-rapportage](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Verificatie methoden-gebruik en inzichten

[Gebruik en inzichten](./howto-authentication-methods-usage-insights.md) bieden inzicht in hoe verificatie methoden voor functies zoals Azure AD MFA en SSPR in uw organisatie werken. Deze rapportage mogelijkheid voorziet uw organisatie van de middelen om inzicht te krijgen in de methoden die worden geregistreerd en hoe ze kunnen worden gebruikt.

### <a name="troubleshoot"></a>Problemen oplossen

* Raadpleeg [problemen met selfservice voor wachtwoord herstel oplossen](./troubleshoot-sspr.md) 

* [Veelgestelde vragen over wachtwoord beheer](./active-directory-passwords-faq.md) volgen 

### <a name="helpful-documentation"></a>Nuttige documentatie

* [Wat zijn verificatiemethoden?](./concept-authentication-methods.md)

* [Hoe werkt het? selfservice voor wachtwoord herstel van Azure AD?](./concept-sspr-howitworks.md)

* [De Azure AD-functionaliteit aanpassen voor Self-service voor wachtwoord herstel](./howto-sspr-customization.md)

* [Wachtwoordbeleid en beperkingen in Azure Active Directory](./concept-sspr-policy.md)

* [Wat is wacht woord terugschrijven?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>Volgende stappen

* Als u aan de slag wilt gaan met de implementatie van SSPR, raadpleegt u [Azure AD selfservice voor wachtwoord herstel inschakelen](tutorial-enable-sspr.md)

* [Implementatie van Azure AD-wachtwoord beveiliging overwegen](./concept-password-ban-bad.md)

* [Overweeg het implementeren van Azure AD Smart-vergren deling](./howto-password-smart-lockout.md)