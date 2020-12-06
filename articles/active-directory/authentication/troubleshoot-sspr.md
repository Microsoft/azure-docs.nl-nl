---
title: Problemen met self-service voor wachtwoord herstel herstellen-Azure Active Directory
description: Meer informatie over het oplossen van veelvoorkomende problemen en oplossings stappen voor selfservice voor wachtwoord herstel in Azure Active Directory
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
ms.openlocfilehash: 7099de23b825fa7af203dff9696bb53e23a099c8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741113"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Problemen met self-service voor wachtwoord herstel in Azure Active Directory oplossen

Met Azure Active Directory (Azure AD) self-service voor wachtwoord herstel (SSPR) kunnen gebruikers hun wacht woord opnieuw instellen in de Cloud.

Als u problemen ondervindt met SSPR, kunnen de volgende probleemoplossings stappen en veelvoorkomende fouten van pas komen. Als u het antwoord op uw probleem niet kunt vinden, [zijn onze ondersteunings teams altijd beschikbaar](#contact-microsoft-support) om u verder te helpen.

## <a name="sspr-configuration-in-the-azure-portal"></a>SSPR-configuratie in de Azure Portal

Als u problemen ondervindt met het bekijken of configureren van SSPR-opties in de Azure Portal, raadpleegt u de volgende stappen voor probleem oplossing:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>De sectie **wacht woord opnieuw instellen** wordt niet weer geven onder Azure AD in de Azure Portal.

Als er geen Azure AD-licentie is toegewezen aan de beheerder die de bewerking uitvoert, ziet u niet of de menu optie **wacht woord opnieuw instellen** wordt weer geven.

Als u een licentie wilt toewijzen aan het beheerders account in kwestie, volgt u de stappen voor het [toewijzen, controleren en oplossen van problemen met licenties](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="i-dont-see-a-particular-configuration-option"></a>Ik zie een bepaalde configuratie optie niet.

Veel elementen van de gebruikers interface zijn verborgen tot ze nodig zijn. Zorg ervoor dat de optie is ingeschakeld voordat u naar de specifieke configuratie opties zoekt.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Het tabblad **on-premises integratie** wordt niet weer geven.

On-premises write-back van wacht woord is alleen zichtbaar als u Azure AD Connect hebt gedownload en de functie hebt geconfigureerd.

Zie [aan de slag met Azure AD Connect](../hybrid/how-to-connect-install-express.md)voor meer informatie.

## <a name="sspr-reporting"></a>SSPR rapportage

Als u problemen ondervindt met SSPR-rapportage in de Azure Portal, raadpleegt u de volgende stappen voor probleem oplossing:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>Ik zie geen activiteitstypen voor wachtwoord beheer in de categorie controle gebeurtenis **selfservice wachtwoord beheer** .

Dit kan gebeuren als er geen Azure AD-licentie is toegewezen aan de beheerder die de bewerking uitvoert.

Als u een licentie wilt toewijzen aan het beheerders account in kwestie, volgt u de stappen voor het [toewijzen, controleren en oplossen van problemen met licenties](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="user-registrations-show-multiple-times"></a>Gebruikers registraties geven meerdere keer weer.

Wanneer een gebruiker zich registreert, registreren we momenteel elke afzonderlijke hoeveelheid gegevens die is geregistreerd als een afzonderlijke gebeurtenis.

Als u deze gegevens wilt samen voegen en meer flexibiliteit hebt in de manier waarop u deze kunt weer geven, kunt u het rapport downloaden en de gegevens openen als een draai tabel in Excel.

## <a name="sspr-registration-portal"></a>SSPR-registratie Portal

Als uw gebruikers problemen ondervinden bij het registreren voor SSPR, raadpleegt u de volgende stappen voor probleem oplossing:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>De map is niet ingeschakeld voor het opnieuw instellen van wacht woorden. De gebruiker ziet mogelijk een fout melding dat het gebruik van deze functie niet is ingeschakeld voor de beheerder.

U kunt SSPR inschakelen voor alle gebruikers, geen gebruikers of voor geselecteerde gebruikers groepen. Er kan via Azure Portal momenteel slechts één Azure AD-groep worden ingeschakeld voor SSPR. Als onderdeel van een bredere implementatie van SSPR worden geneste groepen ondersteund. Zorg ervoor dat aan de gebruikers in de groep(en) die u kiest de juiste licenties zijn toegewezen.

Wijzig in de Azure Portal de configuratie **selfservice voor wacht woord opnieuw instellen** in *geselecteerd* of *alle* en selecteer vervolgens **Opslaan**.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Er is geen Azure AD-licentie toegewezen aan de gebruiker. De gebruiker ziet mogelijk een fout melding dat het gebruik van deze functie niet is ingeschakeld voor de beheerder.

Er kan via Azure Portal momenteel slechts één Azure AD-groep worden ingeschakeld voor SSPR. Als onderdeel van een bredere implementatie van SSPR worden geneste groepen ondersteund. Zorg ervoor dat aan de gebruikers in de groep(en) die u kiest de juiste licenties zijn toegewezen. Bekijk de vorige stap voor probleem oplossing om SSPR in te scha kelen zoals vereist.

Bekijk ook de stappen voor probleem oplossing om ervoor te zorgen dat aan de beheerder die de configuratie-opties uitvoert, een licentie is toegewezen. Als u een licentie wilt toewijzen aan het beheerders account in kwestie, volgt u de stappen voor het [toewijzen, controleren en oplossen van problemen met licenties](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="theres-an-error-processing-the-request"></a>Er is een fout opgetreden bij het verwerken van de aanvraag.

Algemene SSPR registratie fouten kunnen worden veroorzaakt door veel problemen, maar deze fout wordt veroorzaakt door een service storing of een configuratie probleem. [Neem contact op met micro soft ondersteuning](#contact-microsoft-support) voor aanvullende ondersteuning als u deze algemene fout blijft zien wanneer u het SSPR-registratie proces opnieuw probeert uit te voeren.

## <a name="sspr-usage"></a>SSPR gebruik

Als u of uw gebruikers problemen ondervinden met SSPR, raadpleegt u de volgende scenario's voor probleem oplossing en de oplossings stappen:

| Fout | Oplossing |
| --- | --- |
| De map is niet ingeschakeld voor het opnieuw instellen van wacht woorden. | Wijzig in de Azure Portal de configuratie **selfservice voor wacht woord opnieuw instellen** in *geselecteerd* of *alle* en selecteer vervolgens **Opslaan**. |
| Er is geen Azure AD-licentie toegewezen aan de gebruiker. | Dit kan gebeuren als er geen Azure AD-licentie is toegewezen aan de gewenste gebruiker. Als u een licentie wilt toewijzen aan het beheerders account in kwestie, volgt u de stappen voor het [toewijzen, controleren en oplossen van problemen met licenties](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| De map is ingeschakeld voor het opnieuw instellen van wacht woorden, maar de gebruiker heeft ongeldige verificatie-informatie. | Zorg ervoor dat de gebruiker contact gegevens heeft gemaakt in het bestand in de map. Zie [gegevens die worden gebruikt door Azure AD self-service voor wachtwoord herstel](howto-sspr-authenticationdata.md)voor meer informatie. |
| De Directory is ingeschakeld voor wachtwoord herstel, maar de gebruiker heeft slechts één contact gegevens in het bestand wanneer het beleid is ingesteld op twee verificatie methoden vereisen. | Zorg ervoor dat de gebruiker ten minste twee correct geconfigureerde contact methoden heeft. Een voor beeld is een mobiel telefoon nummer *en* een kantoor nummer. |
| De map is ingeschakeld voor het opnieuw instellen van wacht woorden en de gebruiker is op de juiste wijze geconfigureerd, maar er kan geen contact worden gemaakt met de gebruiker. | Dit kan het resultaat zijn van een tijdelijke service fout, of als er onjuiste contact gegevens zijn die niet correct kunnen worden gedetecteerd. <br> <br> Als de gebruiker 10 seconden wacht, wordt een koppeling weer gegeven naar ' Probeer het opnieuw ' en ' Neem contact op met de beheerder '. Als de gebruiker ' opnieuw proberen ' selecteert, wordt de oproep opnieuw geprobeerd. Als de gebruiker ' Neem contact op met de beheerder ', stuurt het een e-mail bericht naar de beheerders die een wachtwoord herstel aanvragen om voor dat gebruikers account te worden uitgevoerd. |
| De gebruiker ontvangt nooit het opnieuw instellen van het wacht woord SMS of de telefoon oproep. | Dit kan het gevolg zijn van een onjuist ingedeeld telefoon nummer in de map. Controleer of het telefoon nummer de notatie ' + 1 4251234567 ' heeft. <br> <br>Het opnieuw instellen van wacht woorden ondersteunt geen extensies, zelfs niet als u er een opgeeft in de map. De uitbrei dingen worden verwijderd voordat de aanroep wordt uitgevoerd. Gebruik een getal zonder extensie of integreer de uitbrei ding in het telefoon nummer in uw PBX (Private Branch Exchange). |
| De gebruiker ontvangt nooit de e-mail voor het opnieuw instellen van een wacht woord. | De meest voorkomende oorzaak van dit probleem is dat het bericht wordt geweigerd door een spam filter. Controleer de map met spam, ongewenste e-mail of verwijderde items voor het e-mail bericht. <br> <br> Zorg er ook voor dat de gebruiker controleert of het juiste e-mail account is geregistreerd bij SSPR. |
| Ik heb een beleid voor het opnieuw instellen van wacht woorden ingesteld, maar wanneer een beheerders account wacht woord opnieuw instellen gebruikt, wordt dat beleid niet toegepast. | Micro soft beheert en beheert het beleid voor het opnieuw instellen van het beheerders wachtwoord om het hoogste beveiligings niveau te garanderen. |
| De gebruiker kan niet te vaak op een dag een wacht woord opnieuw instellen. | Een mechanisme voor automatische beperking wordt gebruikt om te voor komen dat gebruikers hun wacht woord in korte tijd te vaak opnieuw proberen in te stellen. Beperking geldt voor de volgende scenario's: <br><ul><li>De gebruiker probeert een telefoon nummer vijf keer in één uur te valideren.</li><li>De gebruiker probeert de Security vragen Gate vijf keer in één uur te gebruiken.</li><li>De gebruiker probeert vijf keer in één uur een wacht woord opnieuw in te stellen voor hetzelfde gebruikers account.</li></ul>Als een gebruiker dit probleem aantreft, moet deze 24 uur na de laatste poging wachten. De gebruiker kan vervolgens het wacht woord opnieuw instellen. |
| De gebruiker ziet een fout bij het valideren van het telefoon nummer. | Deze fout treedt op wanneer het ingevoerde telefoon nummer niet overeenkomt met het telefoon nummer in het bestand. Zorg ervoor dat de gebruiker het volledige telefoon nummer invoert, inclusief het gebied en de land code, wanneer ze proberen om een op telefoon gebaseerde methode te gebruiken voor het opnieuw instellen van een wacht woord. |
| Er is een fout opgetreden bij het verwerken van de aanvraag. | Algemene SSPR registratie fouten kunnen worden veroorzaakt door veel problemen, maar deze fout wordt veroorzaakt door een service storing of een configuratie probleem. [Neem contact op met micro soft ondersteuning](#contact-microsoft-support) voor aanvullende ondersteuning als u deze algemene fout blijft zien wanneer u het SSPR-registratie proces opnieuw probeert uit te proberen. |
| Schending van on-premises beleid | Het wacht woord voldoet niet aan het on-premises Active Directory wachtwoord beleid. De gebruiker moet een wacht woord definiëren dat voldoet aan de complexiteits-of sterkte vereisten. |
| Het wacht woord voldoet niet aan het beleid voor fuzzy | Het gebruikte wacht woord wordt weer gegeven in de [lijst met uitgesloten wacht woorden](./concept-password-ban-bad.md#how-are-passwords-evaluated) en kan niet worden gebruikt. De gebruiker moet een wacht woord definiëren dat voldoet aan of groter is dan het beleid voor de lijst met geblokkeerde wacht woorden. |

## <a name="sspr-errors-that-a-user-might-see"></a>SSPR fouten die een gebruiker kan zien

De volgende fouten en technische gegevens kunnen aan een gebruiker worden weer gegeven als onderdeel van het SSPR-proces. De fout is vaak niet iets wat ze kunnen oplossen, omdat de SSPR-functie moet inschakelen, geconfigureerd of geregistreerd voor hun account.

Gebruik de volgende informatie om het probleem te begrijpen en wat er moet worden opgelost in de Azure AD-Tenant of het individuele gebruikers account.

| Fout | Details | Technische details |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | U kunt uw wacht woord op dit moment niet opnieuw instellen omdat uw beheerder het opnieuw instellen van het wacht woord voor uw organisatie heeft uitgeschakeld. Er is geen verdere actie die u kunt uitvoeren om deze situatie op te lossen. Neem contact op met uw beheerder en vraag hen om deze functie in te scha kelen.<br /><br />Zie [Help, ik ben mijn wacht woord voor Azure AD verg eten](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions)voor meer informatie. | SSPR_0009: er is vastgesteld dat het opnieuw instellen van het wacht woord niet is ingeschakeld door de beheerder. Neem contact op met uw beheerder en vraag hen het opnieuw instellen van het wacht woord in te scha kelen voor uw organisatie. |
| WritebackNotEnabled = 10 |U kunt uw wacht woord op dit moment niet opnieuw instellen omdat uw beheerder geen benodigde service heeft ingeschakeld voor uw organisatie. Er is geen verdere actie die u kunt uitvoeren om deze situatie op te lossen. Neem contact op met uw beheerder en vraag hen om de configuratie van uw organisatie te controleren.<br /><br />Zie [configureren van wacht woord terugschrijven](./tutorial-enable-sspr-writeback.md)voor meer informatie over deze vereiste service. | SSPR_0010: er is vastgesteld dat het terugschrijven van wacht woorden niet is ingeschakeld. Neem contact op met uw beheerder en vraag hen om wacht woord terugschrijven in te scha kelen. |
| SsprNotEnabledInUserPolicy = 11 | U kunt uw wacht woord op dit moment niet opnieuw instellen omdat uw beheerder het opnieuw instellen van wacht woorden voor uw organisatie niet heeft geconfigureerd. Er is geen verdere actie die u kunt uitvoeren om deze situatie op te lossen. Neem contact op met uw beheerder en vraag deze om het opnieuw instellen van het wacht woord te configureren.<br /><br />Zie voor meer informatie over het opnieuw instellen van wacht woorden [Quick Start: Azure AD self-service password reset](./tutorial-enable-sspr.md). | SSPR_0011: uw organisatie heeft geen beleid voor het opnieuw instellen van wacht woorden gedefinieerd. Neem contact op met uw beheerder en vraag deze om een beleid voor het opnieuw instellen van een wacht woord op te geven. |
| UserNotLicensed = 12 | U kunt uw wacht woord op dit moment niet opnieuw instellen omdat er geen vereiste licenties in uw organisatie ontbreken. Er is geen verdere actie die u kunt uitvoeren om deze situatie op te lossen. Neem contact op met uw beheerder en vraag hen om uw licentie toewijzing te controleren.<br /><br />Zie [licentie vereisten voor Azure AD selfservice voor wacht woord opnieuw instellen](./concept-sspr-licensing.md)voor meer informatie over licentie verlening. | SSPR_0012: uw organisatie beschikt niet over de vereiste licenties om het opnieuw instellen van wacht woorden uit te voeren. Neem contact op met uw beheerder en vraag hen om de licentie toewijzingen te controleren. |
| UserNotMemberOfScopedAccessGroup = 13 | U kunt uw wacht woord op dit moment niet opnieuw instellen omdat uw beheerder uw account niet heeft geconfigureerd voor het gebruik van wacht woord opnieuw instellen. Er is geen verdere actie die u kunt uitvoeren om deze situatie op te lossen. Neem contact op met uw beheerder en vraag hen om uw account te configureren voor het opnieuw instellen van het wacht woord.<br /><br />Zie [Inzoomen op wacht woord opnieuw instellen voor gebruikers](./howto-sspr-deployment.md)voor meer informatie over de account configuratie voor het opnieuw instellen van wacht woorden. | SSPR_0013: u bent geen lid van een groep die is ingeschakeld voor het opnieuw instellen van een wacht woord. Neem contact op met uw beheerder en aanvraag om aan de groep toe te voegen. |
| UserNotProperlyConfigured = 14 | U kunt uw wacht woord op dit moment niet opnieuw instellen omdat de vereiste gegevens ontbreken in uw account. Er is geen verdere actie die u kunt uitvoeren om deze situatie op te lossen. Neem contact op met de beheerder en vraag hen om uw wacht woord opnieuw in te stellen. Nadat u weer toegang hebt tot uw account, moet u de benodigde gegevens registreren.<br /><br />Volg de stappen in het artikel [registreren voor Self-service voor wachtwoord herstel](../user-help/active-directory-passwords-reset-register.md) om informatie te registreren. | SSPR_0014: er zijn aanvullende beveiligings gegevens nodig om uw wacht woord opnieuw in te stellen. Neem contact op met uw beheerder en vraag deze om uw wacht woord opnieuw in te stellen om door te gaan. Nadat u toegang hebt tot uw account, kunt u aanvullende beveiligings gegevens registreren op https://aka.ms/ssprsetup . Uw beheerder kan extra beveiligings gegevens toevoegen aan uw account door de stappen in [set en Read Authentication data voor het opnieuw instellen van het wacht woord](howto-sspr-authenticationdata.md)te volgen. |
| OnPremisesAdminActionRequired = 29 | Uw wacht woord kan niet opnieuw worden ingesteld omdat er een probleem is met de configuratie van het wacht woord voor het opnieuw instellen van uw organisatie. Er is geen verdere actie die u kunt uitvoeren om deze situatie op te lossen. Neem contact op met uw beheerder en vraag deze om te onderzoeken. <br /><br />of<br /><br />We kunnen uw wacht woord op dit moment niet opnieuw instellen vanwege een probleem met de configuratie van het wacht woord voor het opnieuw instellen van uw organisatie. U kunt geen actie ondernemen om dit probleem op te lossen. Neem contact op met uw beheerder en vraag deze om te onderzoeken.<br /><br />Zie [problemen met wacht woord terugschrijven oplossen](troubleshoot-sspr-writeback.md)voor meer informatie over het mogelijke probleem. | SSPR_0029: het wacht woord kan niet opnieuw worden ingesteld vanwege een fout in uw on-premises configuratie. Neem contact op met uw beheerder en vraag deze om te onderzoeken. |
| OnPremisesConnectivityError = 30 | Uw wacht woord kan niet opnieuw worden ingesteld vanwege verbindings problemen met uw organisatie. Er is momenteel geen actie die kan worden uitgevoerd, maar het probleem kan worden opgelost als u het later opnieuw probeert. Als het probleem zich blijft voordoen, neemt u contact op met uw beheerder en vraagt u om te onderzoeken.<br /><br />Zie [problemen met wacht woord terugschrijven oplossen](troubleshoot-sspr-writeback.md)voor meer informatie over verbindings problemen. | SSPR_0030: het wacht woord kan niet opnieuw worden ingesteld vanwege een slechte verbinding met uw on-premises omgeving. Neem contact op met uw beheerder en vraag deze om te onderzoeken.|

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
