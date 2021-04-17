---
title: Gebruiksrechtovereenkomst - Azure Active Directory | Microsoft Docs
description: Ga aan de slag Azure Active Directory gebruiksvoorwaarden om informatie aan werknemers of gasten weer te geven voordat u toegang krijgt.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 01/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c8e18979ff1575e1a050244a96e7858cdce46b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530238"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory gebruiksvoorwaarden

Azure AD-gebruiksvoorwaarden bieden een eenvoudige methode die organisaties kunnen gebruiken om informatie aan eindgebruikers te presenteren. Deze presentatie zorgt ervoor dat gebruikers relevante disclaimers voor juridische vereisten of nalevingsvereisten te zien krijgen. In dit artikel wordt beschreven hoe u aan de slag gaat met beleidsregels voor gebruiksvoorwaarden .

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Overzichtsvideo's

In de volgende video vindt u een kort overzicht van het beleid voor tou's.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Zie voor meer video's:
- [Een gebruiksvoorwaardenbeleid implementeren in Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Een gebruiksvoorwaardenbeleid in een Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Wat kan ik doen met gebruiksvoorwaarden?

Beleidsregels voor azure AD-gebruiksvoorwaarden hebben de volgende mogelijkheden:

- Vereisen dat werknemers of gasten uw gebruiksvoorwaarden accepteren voordat ze toegang krijgen.
- Vereisen dat werknemers of gasten uw gebruiksvoorwaarden op elk apparaat accepteren voordat ze toegang krijgen.
- Vereisen dat werknemers of gasten uw gebruiksvoorwaarden volgens een terugkerend schema accepteren.
- Vereisen dat werknemers of gasten uw gebruiksvoorwaarden accepteren voordat ze beveiligingsgegevens registreren in Azure AD Multi-Factor Authentication (MFA).
- Vereisen dat werknemers uw gebruiksvoorwaarden accepteren voordat ze beveiligingsgegevens registreren in selfservice voor wachtwoord opnieuw instellen (SSPR) van Azure AD.
- Een algemeen gebruiksbeleid presenteren voor alle gebruikers in uw organisatie.
- Specifieke gebruiksvoorwaarden presenteren op basis van gebruikerskenmerken (bijvoorbeeld artsen versus verpleegkundigen of binnenlandse werknemers versus werknemers in het buitenland) met behulp van [dynamische groepen](../enterprise-users/groups-dynamic-membership.md).
- Specifieke gebruiksvoorwaarden presenteren bij het openen van toepassingen met een grote bedrijfsimpact, zoals Salesforce.
- Huidige gebruiksvoorwaarden in verschillende talen.
- Lijst met wie al dan niet heeft ingestemd met uw gebruiksvoorwaardenbeleid.
- Helpen bij het voldoen aan privacyregels.
- Een logboek weergeven van beleidsactiviteiten voor naleving en controle.
- Beleidsregels voor gebruiksvoorwaarden maken en beheren met behulp [Microsoft Graph API's](/graph/api/resources/agreement) (momenteel in preview).

## <a name="prerequisites"></a>Vereisten

Als u beleidsregels voor Azure AD-gebruiksvoorwaarden wilt gebruiken en configureren, hebt u het volgende nodig:

- Abonnement op Azure AD Premium P1, P2, EMS E3 of EMS E5.
   - Als u niet over een van deze abonnementen beschikt, kunt u [Azure AD Premium downloaden](../fundamentals/active-directory-get-started-premium.md) of [Azure AD Premium uitproberen](https://azure.microsoft.com/trial/get-started-active-directory/).
- Een van de volgende beheerdersaccounts voor de directory die u wilt configureren:
   - Hoofdbeheerder
   - Beveiligingsbeheer
   - Beheerder van voorwaardelijke toegang

## <a name="terms-of-use-document"></a>Document met gebruiksrechtovereenkomst

Azure AD-gebruiksvoorwaarden gebruiken de PDF-indeling om inhoud weer te geven. Het PDF-bestand kan van alles bevatten, zoals bestaande contracten, zodat u tijdens het aanmelden van gebruikers eindgebruikersovereenkomsten kunt verzamelen. Ter ondersteuning van gebruikers op mobiele apparaten is de aanbevolen tekengrootte in de PDF 24 punten.

## <a name="add-terms-of-use"></a>Gebruiksvoorwaarden toevoegen

Nadat u het document over uw gebruiksvoorwaardenbeleid hebt afgerond, gebruikt u de volgende procedure om het toe te voegen.

1. Meld u aan bij Azure als globale beheerder, beveiligingsbeheerder of beheerder voor voorwaardelijke toegang.
1. Navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).

    ![Voorwaardelijke toegang - Gebruiksrechtovereenkomst blade](./media/terms-of-use/tou-blade.png)

1. Klik op **Nieuwe voorwaarden**.

    ![Het deelvenster Nieuwe gebruiksvoorwaarden om de instellingen voor de gebruiksvoorwaarden op te geven](./media/terms-of-use/new-tou.png)

1. Voer in **het** vak Naam een naam in voor de gebruiksvoorwaardenbeleid die in de Azure Portal.
1. Voer in **het vak Weergavenaam** een titel in die gebruikers zien wanneer ze zich aanmelden.
1. Voor **Gebruiksrechtovereenkomst document** bladert u naar uw pdf-bestand met de definitieve gebruiksvoorwaarden en selecteert u het.
1. Selecteer de taal voor uw beleidsdocument voor gebruiksvoorwaarden. Met de taaloptie kunt u meerdere gebruiksvoorwaarden uploaden, elk met een andere taal. De versie van de gebruiksvoorwaardenbeleid die een eindgebruiker te zien krijgt, is gebaseerd op de browservoorkeuren.
1. Als u wilt vereisen dat eindgebruikers het gebruiksvoorwaardenbeleid bekijken voordat ze deze accepteren, stelt u Gebruikers **verplichten** de gebruiksvoorwaarden uit te breiden in **op Aan.**
1. Als u wilt vereisen dat eindgebruikers uw gebruiksvoorwaarden accepteren op elk apparaat vanaf welk apparaat ze toegang hebben, stelt u Vereisen dat gebruikers toestemming geven op elk **apparaat** in op **Aan.** Gebruikers moeten mogelijk aanvullende toepassingen installeren als deze optie is ingeschakeld. Zie Gebruiksvoorwaarden per apparaat [voor meer informatie.](#per-device-terms-of-use)
1. Als u toestemmingen voor gebruiksvoorwaarden wilt laten verlopen volgens een schema, stelt u **Verlopen-toestemmingen in** **op Aan.** Als deze instelling is ingesteld op Aan, worden er twee extra schema-instellingen weergegeven.

    ![Instellingen voor verlopen toestemmingen om de begindatum, frequentie en duur in te stellen](./media/terms-of-use/expire-consents.png)

1. Gebruik de **instellingen Beginnen bij verlopen** en **Frequentie** om de planning op te geven voor verloopdatums van het gebruiksbeleid. In de volgende tabel ziet u het resultaat voor een aantal voorbeeldinstellingen:

   | Verlopen vanaf | Frequentie | Resultaat |
   | --- | --- | --- |
   | De datum van vandaag  | Maandelijks | Vanaf vandaag moeten gebruikers de gebruiksvoorwaarden accepteren en vervolgens elke maand opnieuw accepteren. |
   | Datum in de toekomst  | Maandelijks | Vanaf vandaag moeten gebruikers de gebruiksvoorwaarden accepteren. Wanneer de toekomstige datum plaatsvindt, verlopen de toestemmingen en moeten gebruikers deze elke maand opnieuw intrekken.  |

   Als u bijvoorbeeld de vervaldatum vanaf datum in stelt op **1** januari en de frequentie op **Maandelijks,** kunt u als volgende de verloopdatum voor twee gebruikers instellen:

   | Gebruiker | Datum van eerste accepteren | Eerste vervaldatum | Tweede vervaldatum | Derde vervaldatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 1 februari | 1 maart | Apr 1 |
   | Bob | 15 jan | 1 februari | 1 maart | Apr 1 |

1. Gebruik de instelling Duur vóór **reacceptie vereist (dagen)** om het aantal dagen op te geven voordat de gebruiker het gebruiksvoorwaardenbeleid opnieuw moet instellen. Hierdoor kunnen gebruikers hun eigen planning volgen. Als u bijvoorbeeld de duur in stelt op **30** dagen, kunnen er als volgende verloopdatums optreden voor twee gebruikers:

   | Gebruiker | Eerste datum accepteren | Eerste vervaldatum | Tweede vervaldatum | Derde vervaldatum |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 jan | 2 maart | Apr 1 |
   | Bob | 15 jan | 14 februari | 16 maart | Apr 15 |

   Het is mogelijk om de instellingen Expire **consents** en **Duration before reacceptance (dagen)** samen te gebruiken, maar doorgaans gebruikt u een van de twee.

1. Gebruik **onder Voorwaardelijke** toegang de **sjabloonlijst Afdwingen** met beleid voor voorwaardelijke toegang om de sjabloon te selecteren om het gebruiksvoorwaardenbeleid af te dwingen.

    ![Vervolgkeuzelijst Voorwaardelijke toegang om een beleidssjabloon te selecteren](./media/terms-of-use/conditional-access-templates.png)

   | Template | Beschrijving |
   | --- | --- |
   | **Toegang tot cloud-apps voor alle gasten** | Er wordt een beleid voor voorwaardelijke toegang gemaakt voor alle gasten en alle cloud-apps. Dit beleid heeft invloed op de Azure Portal. Zodra deze is gemaakt, moet u zich mogelijk af- en aanmelden. |
   | **Toegang tot cloud-apps voor alle gebruikers** | Er wordt een beleid voor voorwaardelijke toegang gemaakt voor alle gebruikers en alle cloud-apps. Dit beleid heeft invloed op de Azure Portal. Zodra deze is gemaakt, moet u zich af- en aanmelden. |
   | **Aangepast beleid** | Selecteer de gebruikers, groepen en apps op welke gebruikers, groepen en apps deze gebruiksvoorwaarden worden toegepast. |
   | **Beleid voor voorwaardelijke toegang later maken** | Deze gebruiksvoorwaarden worden weergegeven in de lijst met toekenningscontroles bij het maken van een beleid voor voorwaardelijke toegang. |

   >[!IMPORTANT]
   >Beleidscontroles voor voorwaardelijke toegang (inclusief beleidsregels voor gebruiksvoorwaarden) bieden geen ondersteuning voor afdwinging op serviceaccounts. U wordt aangeraden alle serviceaccounts uit te sluiten van het beleid voor voorwaardelijke toegang.

    Aangepaste beleidsregels voor voorwaardelijke toegang maken gedetailleerde gebruiksvoorwaarden mogelijk, tot een specifieke cloudtoepassing of groep gebruikers. Zie Quickstart: Vereisen dat gebruiksvoorwaarden worden geaccepteerd voordat u toegang krijgt [tot cloud-apps voor meer informatie.](require-tou.md)

1. Klik op **Create**.

    Als u een aangepaste sjabloon voor voorwaardelijke toegang hebt geselecteerd, wordt er een nieuw scherm weergegeven waarmee u het aangepaste beleid voor voorwaardelijke toegang kunt maken.

   ![Nieuw deelvenster Voorwaardelijke toegang als u de aangepaste beleidssjabloon voor voorwaardelijke toegang hebt gekozen](./media/terms-of-use/custom-policy.png)

   U ziet nu uw nieuwe beleidsregels voor gebruiksvoorwaarden.

   ![Nieuwe gebruiksvoorwaarden die worden vermeld in de gebruiksvoorwaardenblade](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Rapport weergeven van wie heeft geaccepteerd en geweigerd

In de Gebruiksrechtovereenkomst-blade ziet u de aantallen gebruikers die al dan niet hebben geaccepteerd. Deze tellingen en wie heeft geaccepteerd/geweigerd, worden opgeslagen voor de levensduur van het gebruiksvoorwaardenbeleid.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).

    ![Gebruiksrechtovereenkomst blade met het aantal gebruikers dat is geaccepteerd en geweigerd](./media/terms-of-use/view-tou.png)

1. Klik voor een gebruiksvoorwaardenbeleid op de getallen onder **Geaccepteerd** of Geweigerd **om** de huidige status voor gebruikers weer te geven.

    ![Gebruiksrechtovereenkomst deelvenster toestemmingen met de gebruikers die hebben geaccepteerd](./media/terms-of-use/accepted-tou.png)

1. Als u de geschiedenis voor een afzonderlijke gebruiker wilt weergeven, klikt u op het beletselteken (**...**) en vervolgens **op Geschiedenis weergeven.**

    ![Contextmenu Geschiedenis voor een gebruiker weergeven](./media/terms-of-use/view-history-menu.png)

   In het deelvenster Geschiedenis weergeven ziet u een geschiedenis van alle accepteren, weigeren en verlopen.

   ![Het deelvenster Geschiedenis weergeven bevat de geschiedenis accepteert, weigert en verloopt voor een gebruiker](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD-auditlogboeken weergeven

Als u aanvullende activiteiten wilt weergeven, bevat het beleid voor gebruiksvoorwaarden van Azure AD auditlogboeken. Elke toestemming van de gebruiker activeert een gebeurtenis in de auditlogboeken die **30 dagen worden opgeslagen.** U kunt deze logboeken bekijken in de portal of downloaden als CSV-bestand.

Gebruik de volgende procedure om aan de slag te gaan met Azure AD-auditlogboeken:

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer een gebruiksvoorwaardenbeleid.
1. Klik op **Auditlogboeken weergeven**.

    ![Gebruiksrechtovereenkomst blade met de optie Auditlogboeken weergeven gemarkeerd](./media/terms-of-use/audit-tou.png)

1. In het scherm Azure AD-auditlogboeken kunt u de informatie filteren met behulp van de opgegeven lijsten om specifieke auditlogboekgegevens te targeten.

    U kunt ook op **Downloaden** klikken om de informatie te downloaden naar een CSV-bestand voor lokaal gebruik.

   ![Scherm met auditlogboeken van Azure AD met de datum, het doelbeleid, geïnitieerd door en activiteit](./media/terms-of-use/audit-logs-tou.png)

   Als u op een logboek klikt, wordt er een deelvenster weergegeven met aanvullende activiteitsdetails.

   ![Activiteitsdetails voor een logboek met activiteiten, activiteitsstatus, geïnitieerd door, doelbeleid](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Hoe de gebruiksvoorwaarden eruitzien voor gebruikers

Zodra een tou-beleid is gemaakt en afgedwongen, zien gebruikers, die binnen het bereik vallen, het volgende scherm tijdens het aanmelden.

![Voorbeeld van gebruiksvoorwaarden die worden weergegeven wanneer een gebruiker zich aan meldt](./media/terms-of-use/user-tou.png)

Gebruikers kunnen de gebruiksvoorwaarden bekijken en, indien nodig, knoppen gebruiken om in en uit te zoomen.

![Weergave van gebruiksvoorwaarden met zoomknoppen](./media/terms-of-use/zoom-buttons.png)

In het volgende scherm ziet u hoe een beleid voor gebruik op mobiele apparaten eruitziet.

![Voorbeeld van gebruiksvoorwaarden die worden weergegeven wanneer een gebruiker zich op een mobiel apparaat meldt](./media/terms-of-use/mobile-tou.png)

Gebruikers moeten de gebruiksvoorwaarden slechts eenmaal accepteren en ze zien de gebruiksvoorwaarden niet meer bij volgende aanmeldingen.

### <a name="how-users-can-review-their-terms-of-use"></a>Hoe gebruikers hun gebruiksvoorwaarden kunnen controleren

Gebruikers kunnen de gebruiksvoorwaarden bekijken en zien die ze hebben geaccepteerd met behulp van de volgende procedure.

1. Meld u aan bij [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Klik in de rechterbovenhoek op uw naam en selecteer **Profiel.**

    ![MyApps-site met het deelvenster van de gebruiker geopend](./media/terms-of-use/tou14.png)

1. Klik in uw profielpagina op **Gebruiksvoorwaarden controleren**.

    ![Profielpagina voor een gebruiker met de koppeling Gebruiksvoorwaarden controleren](./media/terms-of-use/tou13a.png)

1. Hier kunt u de gebruiksvoorwaarden bekijken die u hebt geaccepteerd.

## <a name="edit-terms-of-use-details"></a>Gebruiksvoorwaarden bewerken

U kunt enkele details van beleidsregels voor gebruiksvoorwaarden bewerken, maar u kunt een bestaand document niet wijzigen. In de volgende procedure wordt beschreven hoe u de details bewerkt.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiksvoorwaarden die u wilt bewerken.
1. Klik **op Voorwaarden bewerken.**
1. In het deelvenster Gebruiksvoorwaarden bewerken kunt u het volgende wijzigen:
    - **Naam:** dit is de interne naam van de tou die niet wordt gedeeld met eindgebruikers
    - **Weergavenaam:** dit is de naam die eindgebruikers kunnen zien bij het weergeven van de gebruiksnaam
    - **Gebruikers verplichten om de gebruiksvoorwaarden** uit te breiden: als u deze instelling instelt op **Aan,** wordt het eindgebruik geforcerd om het document over de gebruiksvoorwaarden uit te breiden voordat het wordt geaccepteerd.
    - (Preview) U kunt **een bestaand gebruiksvoorwaardendocument** bijwerken
    - U kunt een taal toevoegen aan een bestaande gebruikstoevoeging

   Als er andere instellingen zijn die u wilt wijzigen, zoals pdf-documenten, moeten gebruikers toestemming geven op elk apparaat, toestemming laten verlopen, de duur vóór de reaccepttie of het beleid voor voorwaardelijke toegang, moet u een nieuw beleid voor gebruiksduur maken.

    ![Bewerken met verschillende taalopties ](./media/terms-of-use/edit-terms-use.png)

1. Wanneer u klaar bent, klikt u op **Opslaan om** uw wijzigingen op te slaan.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>De versie of PDF van een bestaande gebruiksvoorwaarden bijwerken

1.  Meld u aan bij Azure en navigeer [naar Gebruiksrechtovereenkomst](https://aka.ms/catou)
2.  Selecteer de gebruiksvoorwaarden die u wilt bewerken.
3.  Klik **op Voorwaarden bewerken.**
4.  Voor de taal die u wilt bijwerken van een nieuwe versie, klikt u **op Bijwerken** onder de actiekolom

    ![Deelvenster Gebruiksvoorwaarden bewerken met opties voor naam en uitv](./media/terms-of-use/edit-terms-use.png)

5.  Upload in het deelvenster aan de rechterkant het PDF-bestand voor de nieuwe versie
6.  Er is hier ook een schakeloptie **Reaccept** vereisen als u wilt vereisen dat uw gebruikers deze nieuwe versie accepteren wanneer ze zich de volgende keer aanmelden. Als u wilt dat uw gebruikers zich opnieuw accepteren, wordt ze de volgende keer dat ze proberen toegang te krijgen tot de resource die is gedefinieerd in uw beleid voor voorwaardelijke toegang gevraagd om deze nieuwe versie te accepteren. Als u niet vereist dat uw gebruikers deze opnieuw intrekken, blijft hun eerdere toestemming actueel en zien alleen nieuwe gebruikers die geen toestemming hebben gegeven vóór of van wie de toestemming verloopt de nieuwe versie.

    ![Optie voor opnieuw accepteren van gebruiksvoorwaarden bewerken gemarkeerd](./media/terms-of-use/re-accept.png)

7.  Nadat u uw nieuwe PDF-bestand hebt geüpload en opnieuw hebt besloten om het bestand opnieuw in te voegen, klikt u op Toevoegen onderaan het deelvenster.
8.  U ziet nu de meest recente versie onder de kolom Document.

## <a name="view-previous-versions-of-a-tou"></a>Vorige versies van een gebruiksversie weergeven

1.  Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op https://aka.ms/catou.
2.  Selecteer de gebruiksvoorwaarden waarvoor u een versiegeschiedenis wilt weergeven.
3.  Klik op **Talen en versiegeschiedenis**
4.  Klik op **Vorige versies bekijken.**

    ![documentdetails, inclusief taalversies](./media/terms-of-use/document-details.png)

5.  U kunt op de naam van het document klikken om die versie te downloaden

## <a name="see-who-has-accepted-each-version"></a>Zien wie elke versie heeft geaccepteerd

1.  Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op https://aka.ms/catou.
2.  Als u wilt zien wie de toU momenteel heeft geaccepteerd, klikt u op het nummer onder de kolom **Geaccepteerd** voor de want-tou.
3.  Standaard wordt op de volgende pagina de huidige status weergegeven van elke gebruikersacceptatie voor de gebruiksrechten
4.  Als u de vorige toestemmingsgebeurtenissen wilt zien, kunt u Alle **selecteren** in de **vervolgkeuzekeuze** selecteren Huidige status. U kunt nu alle gebruikersgebeurtenissen bekijken in details over elke versie en wat er is gebeurd.
5.  U kunt ook een specifieke versie  selecteren in de vervolgkeuzekeuze selecteren om te zien wie die specifieke versie heeft geaccepteerd.


## <a name="add-a-tou-language"></a>Een tou-taal toevoegen

In de volgende procedure wordt beschreven hoe u een toU-taal toevoegt.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiksvoorwaarden die u wilt bewerken.
1. Klik **op Voorwaarden bewerken**
1. Klik **onder aan** de pagina op Taal toevoegen.
1. Upload uw gelokaliseerde PDF-bestand in het deelvenster Gebruiksvoorwaarden toevoegen en selecteer de taal.

    ![Gebruiksrechtovereenkomst geselecteerd en wordt het tabblad Talen weergegeven in het detailvenster](./media/terms-of-use/select-language.png)

1. Klik **op Taal toevoegen.**
1. Klik op **Opslaan**.

1. Klik **op Toevoegen** om de taal toe te voegen.

## <a name="per-device-terms-of-use"></a>Gebruiksvoorwaarden per apparaat

Met **de instelling Gebruikers verplichten** om toestemming te geven voor elk apparaat kunt u vereisen dat eindgebruikers uw gebruiksvoorwaarden accepteren op elk apparaat waarmee ze toegang hebben. De eindgebruiker moet zijn apparaat registreren bij Azure AD. Wanneer het apparaat is geregistreerd, wordt de apparaat-id gebruikt om het gebruiksvoorwaardenbeleid op elk apparaat af te dwingen.

Hier is een lijst met de ondersteunde platforms en software.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Anders |
> | --- | --- | --- | --- | --- |
> | **Systeemeigen app** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (met extensie)** | Ja | Ja | Ja |  |

Gebruiksvoorwaarden per apparaat hebben de volgende beperkingen:

- Een apparaat kan slechts aan één tenant worden samengevoegd.
- Een gebruiker moet machtigingen hebben om het apparaat toe te sluiten.
- De Intune-inschrijvings-app wordt niet ondersteund. Zorg ervoor dat dit beleid is uitgesloten van beleid voor voorwaardelijke toegang waarvoor beleid voor gebruiksvoorwaarden is vereist.
- Azure AD B2B-gebruikers worden niet ondersteund.

Als het apparaat van de gebruiker niet is samengevoegd, ontvangen ze een bericht dat ze hun apparaat moeten aansluiten. Hun ervaring is afhankelijk van het platform en de software.

### <a name="join-a-windows-10-device"></a>Een apparaat met Windows 10 toevoegen

Als een gebruiker een Windows 10 en Microsoft Edge gebruikt, ontvangt deze een bericht dat vergelijkbaar is met het volgende om het [apparaat toe te sluiten.](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)

![Windows 10 en Microsoft Edge: een bericht dat aangeeft dat uw apparaat moet worden geregistreerd](./media/terms-of-use/per-device-win10-edge.png)

Als ze Chrome gebruiken, wordt hen gevraagd de extensie [Windows 10 Accounts te installeren.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)

### <a name="register-an-ios-device"></a>Een iOS-apparaat registreren

Als een gebruiker een iOS-apparaat gebruikt, wordt hij gevraagd om de app [Microsoft Authenticator installeren.](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="register-an-android-device"></a>Een Android-apparaat registreren

Als een gebruiker een Android-apparaat gebruikt, wordt hij gevraagd om de app [Microsoft Authenticator installeren.](https://play.google.com/store/apps/details?id=com.azure.authenticator)

### <a name="browsers"></a>Browsers

Als een gebruiker een browser gebruikt die niet wordt ondersteund, wordt hij gevraagd een andere browser te gebruiken.

![Bericht dat aangeeft dat uw apparaat moet worden geregistreerd, maar de browser wordt niet ondersteund](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Gebruiksvoorwaarden verwijderen

U kunt oude gebruiksvoorwaarden verwijderen met behulp van de volgende procedure.

1. Meld u aan bij Azure en navigeer naar **Gebruiksrechtovereenkomst** op [https://aka.ms/catou](https://aka.ms/catou).
1. Selecteer de gebruiksvoorwaarden die u wilt verwijderen.
1. Klik op **Gebruiksvoorwaarden verwijderen**.
1. Klik in het bericht waarin u wordt gevraagd of u wilt doorgaan op **Ja**.

    ![Bericht waarin wordt gevraagd om bevestiging om gebruiksvoorwaarden te verwijderen](./media/terms-of-use/delete-tou.png)

   Als het goed is, ziet u uw gebruiksvoorwaardenbeleid niet meer.

## <a name="user-acceptance-record-deletion"></a>Record voor gebruikersacceptatie verwijderen

Records voor gebruikersacceptatie worden verwijderd:

- Wanneer de beheerder de gebruiksrechten expliciet verwijdert. Als dit gebeurt, worden ook alle acceptatierecords verwijderd die aan die specifieke gebruiksgegevens zijn gekoppeld.
- Wanneer de tenant de licentie voor Azure Active Directory Premium verliest.
- Wanneer de tenant wordt verwijderd.

## <a name="policy-changes"></a>Beleidswijzigingen

Beleid voor voorwaardelijke toegang wordt onmiddellijk van kracht. Als dit gebeurt, ziet de beheerder 'helaas clouds' of 'Problemen met Azure AD-token'. De beheerder moet zich af- en weer aanmelden om te voldoen aan het nieuwe beleid.

> [!IMPORTANT]
> Gebruikers in het bereik moeten zich af- en aanmelden om te voldoen aan een nieuw beleid:
>
> - een beleid voor voorwaardelijke toegang is ingeschakeld voor een gebruiksvoorwaardenbeleid
> - of er wordt een tweede gebruiksvoorwaardenbeleid gemaakt

## <a name="b2b-guests"></a>B2B-gasten

De meeste organisaties hebben een proces voor hun werknemers om toestemming te geven voor de gebruiksvoorwaarden en privacyverklaringen van hun organisatie. Maar hoe kunt u dezelfde toestemmingen afdwingen voor B2B-gasten (Business-to-Business) van Azure AD wanneer ze worden toegevoegd via SharePoint of Teams? Met behulp van beleidsregels voor voorwaardelijke toegang en gebruiksvoorwaarden kunt u een beleid rechtstreeks afdwingen voor B2B-gastgebruikers. Tijdens de stroom voor het inwisselen van de uitnodiging krijgt de gebruiker de gebruiksvoorwaarden te zien. Deze ondersteuning is momenteel beschikbaar als preview-versie.

Gebruiksrechtovereenkomst beleidsregels worden alleen weergegeven wanneer de gebruiker een gastaccount in Azure AD heeft. SharePoint Online heeft momenteel [een ad-hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) ervaring voor externe ontvangers voor delen om een document of map te delen waarvoor de gebruiker geen gastaccount hoeft te hebben. In dit geval wordt er geen gebruiksvoorwaardenbeleid weergegeven.

![Deelvenster Gebruikers en groepen: tabblad Opnemen met de optie Alle gastgebruikers ingeschakeld](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Ondersteuning voor cloud-apps

Gebruiksrechtovereenkomst kunnen worden gebruikt voor verschillende cloud-apps, zoals Azure Information Protection en Microsoft Intune. Deze ondersteuning is momenteel beschikbaar als preview-versie.

### <a name="azure-information-protection"></a>Azure Information Protection

U kunt een beleid voor voorwaardelijke toegang configureren voor de Azure Information Protection app en een gebruiksvoorwaardenbeleid vereisen wanneer een gebruiker een beveiligd document gebruikt. Hiermee activeert u een gebruiksvoorwaardenbeleid voordat een gebruiker voor de eerste keer toegang heeft tot een beveiligd document.

![Deelvenster Cloud-apps met Microsoft Azure Information Protection app geselecteerd](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune inschrijving

U kunt een beleid voor voorwaardelijke toegang configureren voor de Microsoft Intune-inschrijvings-app en een gebruiksvoorwaardenbeleid vereisen voordat een apparaat in Intune wordt ingeschreven. Zie de blogpost Read Choosing the right Terms solution for your organization (De juiste voorwaarden kiezen [voor uw organisatie) voor meer informatie.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Deelvenster Cloud-apps met Microsoft Intune app geselecteerd](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> De Intune-inschrijvings-app wordt niet ondersteund voor [gebruiksvoorwaarden per apparaat.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Ik kan me niet aanmelden met PowerShell wanneer gebruiksvoorwaarden zijn ingeschakeld.**<br />
A: Gebruiksrechtovereenkomst kunnen alleen worden geaccepteerd bij interactieve authenticatie.

**V: Hoe kan ik zien wanneer/of een gebruiker een gebruiksvoorwaarden heeft geaccepteerd?**<br />
A: Klik op de Gebruiksrechtovereenkomst op het getal onder **Geaccepteerd.** U kunt de accept-activiteit ook bekijken of doorzoeken in de Auditlogboeken van Azure AD. Zie Rapport weergeven van wie heeft geaccepteerd en geweigerd en [Azure AD-auditlogboeken weergeven voor meer informatie.](#view-azure-ad-audit-logs)

**V: hoe lang worden gegevens opgeslagen?**<br />
A: De gebruiker telt in het gebruiksvoorwaardenrapport en wie heeft geaccepteerd/geweigerd, worden opgeslagen voor de levensduur van de gebruiksvoorwaarden. De Azure AD-auditlogboeken worden 30 dagen opgeslagen.

**V: Waarom zie ik een ander aantal toestemmingen in de gebruiksvoorwaardenrapport vergeleken met de Azure AD-auditlogboeken?**<br />
A: De gebruiksvoorwaarden worden opgeslagen voor de levensduur van dat gebruiksbeleid, terwijl de Azure AD-auditlogboeken 30 dagen worden opgeslagen. Bovendien worden in het gebruiksvoorwaardenrapport alleen de huidige toestemmingstoestand van de gebruiker weergegeven. Als een gebruiker bijvoorbeeld weigert en vervolgens accepteert, wordt in het gebruiksvoorwaardenrapport alleen het accepteren van die gebruiker weer geven. Als u de geschiedenis wilt zien, kunt u de Azure AD-auditlogboeken gebruiken.

**V: Als ik de details voor een gebruiksvoorwaardenbeleid bewerk, moeten gebruikers deze dan opnieuw accepteren?**<br />
A: Nee, als een beheerder de details bewerkt voor een gebruiksvoorwaardenbeleid (naam, weergavenaam, gebruikers verplichten om uit te breiden of een taal toe te voegen), is het niet nodig dat gebruikers de nieuwe voorwaarden opnieuw in kunnen gaan.

**V: Kan ik een bestaand beleidsdocument voor de gebruiksvoorwaarden bijwerken?**<br />
A: Momenteel kunt u een bestaand beleidsdocument voor de gebruiksvoorwaarden niet bijwerken. Als u een beleidsdocument voor de gebruiksvoorwaarden wilt wijzigen, moet u een nieuwe gebruiksvoorwaardenbeleids-instantie maken.

**V: Als hyperlinks in het PDF-document over het gebruiksbeleid staan, kunnen eindgebruikers er dan op klikken?**<br />
A: Ja, eindgebruikers kunnen hyperlinks naar extra pagina's selecteren, maar koppelingen naar secties in het document worden niet ondersteund. Hyperlinks in gebruiksbeleids-PDF's werken ook niet wanneer ze worden geopend vanuit de Azure AD MyApps/MyAccount-portal.

**V: Ondersteunt een gebruiksvoorwaardenbeleid meerdere talen?**<br />
A: Ja. Er zijn momenteel 108 verschillende talen die een beheerder kan configureren voor één gebruiksvoorwaardenbeleid. Een beheerder kan meerdere PDF-documenten uploaden en deze documenten taggen met een bijbehorende taal (maximaal 108). Wanneer eindgebruikers zich aanmelden, kijken we naar hun browsertaalvoorkeur en geven we het bijbehorende document weer. Als er geen overeenkomst is, wordt het standaarddocument weergegeven. Dit is het eerste document dat wordt geüpload.

**V: Wanneer wordt het beleid voor de gebruiksvoorwaarden geactiveerd?**<br />
A: De gebruiksvoorwaarden worden geactiveerd tijdens het aanmelden.

**V: Op welke toepassingen kan ik een gebruiksvoorwaardenbeleid richten?**<br />
A: U kunt een beleid voor voorwaardelijke toegang maken voor de bedrijfstoepassingen met moderne verificatie. Zie [Bedrijfstoepassingen](./../manage-apps/view-applications-portal.md) voor meer informatie.

**V: Kan ik meerdere beleidsregels voor gebruiksvoorwaarden toevoegen aan een bepaalde gebruiker of app?**<br />
A: Ja, door meerdere beleidsregels voor voorwaardelijke toegang te maken die zijn gericht op deze groepen of toepassingen. Als gebruikers binnen het bereik van meerdere gebruiksvoorwaarden vallen, accepteren ze één gebruiksvoorwaardenbeleid per keer.

**V: Wat gebeurt er als een gebruiker de gebruiksvoorwaarden weigert?**<br />
A: De gebruiker krijgt dan geen toegang tot de toepassing. De gebruiker moet zich opnieuw aanmelden en de voorwaarden accepteren om toegang te krijgen.

**V: Is het mogelijk om een eerder geaccepteerd gebruiksvoorwaardenbeleid niet te accepteren?**<br />
A: U kunt [eerder geaccepteerde](#how-users-can-review-their-terms-of-use)beleidsregels voor gebruiksvoorwaarden bekijken, maar op dit moment is er geen manier om het beleid niet te accepteren.

**V: Wat gebeurt er als ik ook de voorwaarden van Intune gebruik?**<br />
A: Als u zowel de gebruiksvoorwaarden van Azure AD als [de Intune-voorwaarden](/intune/terms-and-conditions-create)hebt geconfigureerd, moet de gebruiker beide accepteren. Zie het [blogbericht Choosing the right Terms solution for your organization (De juiste voorwaarden](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)voor uw organisatie kiezen) voor meer informatie.

**V: Welke eindpunten gebruiken de gebruiksvoorwaarden voor verificatie?**<br />
A: Gebruiksrechtovereenkomst maakt gebruik van de volgende eindpunten voor https://tokenprovider.termsofuse.identitygovernance.azure.com verificatie: en https://account.activedirectory.windowsazure.com . Als uw organisatie een lijst met toegestane URL's voor inschrijving heeft, moet u deze eindpunten toevoegen aan uw lijst met toegestane url's, samen met de Azure AD-eindpunten voor aanmelding.

## <a name="next-steps"></a>Volgende stappen

- [Quickstart: Vereisen dat gebruiksvoorwaarden worden geaccepteerd voor toegang tot cloud-apps](require-tou.md)
