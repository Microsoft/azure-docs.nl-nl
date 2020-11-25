---
title: 'Azure AD Connect: de voorkeurs locatie van gegevens voor Microsoft 365 resources configureren'
description: Hierin wordt beschreven hoe u uw Microsoft 365 gebruikers bronnen dicht bij de gebruiker kunt plaatsen met Azure Active Directory Connect synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c456e7788280b7ca5328342e1cd848ba3a583a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972756"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Active Directory Connect synchronisatie: de voorkeurs locatie van gegevens voor Microsoft 365 resources configureren
Het doel van dit onderwerp is om u stapsgewijs te begeleiden bij het configureren van het kenmerk voor de voorkeurs locatie van gegevens in Azure Active Directory (Azure AD) Connect Sync. Wanneer iemand gebruikmaakt van mogelijkheden voor meerdere geografische locaties in Microsoft 365, gebruikt u dit kenmerk om de geografische locatie van de Microsoft 365 gegevens van de gebruiker aan te duiden. (De termen *regio* en *geo* worden door elkaar gebruikt.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Synchronisatie van voorkeurs locatie van gegevens inschakelen
Microsoft 365 resources voor uw gebruikers bevinden zich standaard in dezelfde geografische regio als uw Azure AD-Tenant. Als uw Tenant zich bijvoorbeeld bevindt in Noord-Amerika, bevinden de Exchange-post vakken van de gebruikers zich ook in Noord-Amerika. Voor een Multi-National organisatie is dit mogelijk niet optimaal.

Door het kenmerk **preferredDataLocation** in te stellen, kunt u de geo van een gebruiker definiëren. U kunt de Microsoft 365 resources van de gebruiker, zoals het postvak en OneDrive, in dezelfde geografische regio als de gebruiker hebben en nog steeds één Tenant voor uw hele organisatie hebben.

> [!IMPORTANT]
> Multi-geo is momenteel beschikbaar voor klanten met een actief Enterprise Agreement en een minimum van 250 Microsoft 365 Services-abonnementen. Neem contact op met uw micro soft-vertegenwoordiger voor meer informatie.
>
>

Een lijst met alle geografische gebieden voor Microsoft 365 vindt u in waar bevinden zich [uw gegevens?](/microsoft-365/enterprise/o365-data-locations).

De geografische gebieden in Microsoft 365 beschikbaar voor meerdere geografische locaties zijn:

| Geo | waarde preferredDataLocation |
| --- | --- |
| Azië en Stille Oceaan | APC |
| Australië | AU'S |
| Canada | KUNNEN |
| Europese Unie | EUR |
| Frankrijk | FRA |
| India | IND |
| Japan | JPN |
| Korea | KOR |
| Zuid-Afrika | ZAF |
| Zwitserland | OPSLAAN |
| Verenigde Arabische Emiraten | ZIJN |
| Verenigd Koninkrijk | GBR |
| Verenigde Staten | Vietnam |

* Als een geo-waarde niet wordt vermeld in deze tabel (bijvoorbeeld Zuid-Amerika), kan deze niet worden gebruikt voor multi-geo.

* Niet alle Microsoft 365 werk belastingen ondersteunen het gebruik van het instellen van de geo-waarde van een gebruiker.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect ondersteuning voor synchronisatie

Azure AD Connect ondersteunt synchronisatie van het kenmerk **preferredDataLocation** voor **gebruikers** objecten in versie 1.1.524.0 en hoger. Specifiek:

* Het schema van de **gebruiker** van het object type in de Azure AD-connector wordt uitgebreid met het kenmerk **preferredDataLocation** . Het kenmerk is van het type teken reeks met één waarde.
* Het schema van de **persoon** van het object type in de tekst wordt uitgebreid met het kenmerk **preferredDataLocation** . Het kenmerk is van het type teken reeks met één waarde.

**PreferredDataLocation** is standaard niet ingeschakeld voor synchronisatie. Deze functie is bedoeld voor grotere organisaties. Het Active Directory schema in Windows Server 2019 heeft een kenmerk **msDS-preferredDataLocation** die u voor dit doel moet gebruiken. Als u het Active Directory schema niet hebt bijgewerkt en dit niet kunt doen, moet u een kenmerk identificeren om het Microsoft 365 geo voor uw gebruikers op te slaan. Dit is voor elke organisatie anders.

> [!IMPORTANT]
> Met Azure AD kan het kenmerk **preferredDataLocation** op **Cloud gebruikers objecten** rechtstreeks worden geconfigureerd met behulp van Azure AD Power shell. Als u dit kenmerk wilt configureren voor **gesynchroniseerde gebruikers objecten**, moet u Azure AD Connect gebruiken.

Voordat u de synchronisatie inschakelt:

* Als u het Active Directory-schema niet hebt bijgewerkt naar 2019, moet u bepalen welk Active Directory kenmerk op locatie voor gebruik als bron kenmerk. Deze moet van het type **teken reeks met één waarde** zijn.
* Als u eerder het kenmerk **preferredDataLocation** op bestaande **gesynchroniseerde gebruikers objecten** in azure AD hebt geconfigureerd met behulp van Azure AD Power shell, moet u de kenmerk waarden backport naar de bijbehorende **gebruikers** objecten in on-premises Active Directory.

    > [!IMPORTANT]
    > Als u deze waarden niet backport, verwijdert Azure AD Connect de bestaande kenmerk waarden in azure AD wanneer synchronisatie voor het kenmerk **preferredDataLocation** is ingeschakeld.

* Configureer het bron kenmerk op ten minste een aantal on-premises Active Directory gebruikers objecten nu. U kunt dit later voor verificatie gebruiken.

In de volgende secties vindt u de stappen voor het inschakelen van synchronisatie van het kenmerk **preferredDataLocation** .

> [!NOTE]
> De stappen worden beschreven in de context van een Azure AD-implementatie met een topologie met één forest en zonder aangepaste synchronisatie regels. Als u een topologie met meerdere forests hebt, aangepaste synchronisatie regels hebt geconfigureerd of een staging-server hebt, moet u de stappen dienovereenkomstig aanpassen.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Stap 1: de synchronisatie planner uitschakelen en controleren of er geen synchronisatie wordt uitgevoerd
Om te voor komen dat onbedoelde wijzigingen worden geëxporteerd naar Azure AD, moet u ervoor zorgen dat er geen synchronisatie plaatsvindt terwijl u de synchronisatie regels bijwerkt. De ingebouwde synchronisatie planner uitschakelen:

1. Start een Power shell-sessie op de Azure AD Connect-server.
2. Schakel de geplande synchronisatie uit door deze cmdlet uit te voeren: `Set-ADSyncScheduler -SyncCycleEnabled $false` .
3. Start de **Synchronization Service Manager** door de **START**  >  **synchronisatie service** te starten.
4. Selecteer het tabblad **bewerkingen** en controleer of er geen bewerking is met de status *in behandeling*.

![Scherm opname van Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Stap 2: het schema voor Active Directory vernieuwen
Als u het Active Directory schema hebt bijgewerkt naar 2019 en Connect is geïnstalleerd vóór de schema-uitbrei ding, heeft de Connect-schema cache geen bijgewerkt schema. Vervolgens moet u het schema vernieuwen vanuit de wizard zodat het wordt weer gegeven in de gebruikers interface.

1. Start de wizard Azure AD Connect op het bureau blad.
2. Selecteer de optie **Directory schema vernieuwen** en klik op **volgende**.
3. Voer uw Azure AD-referenties in en klik op **volgende**.
4. Zorg ervoor dat alle forests zijn geselecteerd op de pagina **Directory schema vernieuwen** en klik op **volgende**.
5. Wanneer u klaar bent, sluit u de wizard.

![Scherm opname van Directory-schema vernieuwen in de wizard verbinding maken](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Stap 3: het bron kenmerk toevoegen aan het on-premises Active Directory-Connector schema
**Deze stap is alleen nodig als u Connect versie 1.3.21 of ouder uitvoert. Als u zich op 1.4.18 of nieuwer bevindt, gaat u verder met stap 5.**  
Niet alle Azure AD-kenmerken worden geïmporteerd in de on-premises Active Directory-Connector ruimte. Als u ervoor hebt gekozen om een kenmerk te gebruiken dat standaard niet is gesynchroniseerd, moet u het importeren. Om het bron kenmerk toe te voegen aan de lijst met geïmporteerde kenmerken:

1. Selecteer het tabblad **connectors** in de Synchronization Service Manager.
2. Klik met de rechter muisknop op de on-premises Active Directory-connector en selecteer **Eigenschappen**.
3. Ga in het pop-upvenster naar het tabblad **kenmerken selecteren** .
4. Zorg ervoor dat het bron kenmerk dat u hebt geselecteerd, is ingeschakeld in de lijst met kenmerken. Als uw kenmerk niet wordt weer gegeven, selecteert u het selectie vakje **alles weer geven** .
5. Selecteer **OK** om op te slaan.

![Scherm opname van het dialoog venster Synchronization Service Manager en eigenschappen waarin de lijst ' kenmerken ' is gemarkeerd.](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Stap 4: **PreferredDataLocation** toevoegen aan het Azure AD-connector schema
**Deze stap is alleen nodig als u Connect versie 1.3.21 of ouder uitvoert. Als u zich op 1.4.18 of nieuwer bevindt, gaat u verder met stap 5.**  
Het kenmerk **preferredDataLocation** wordt standaard niet in de Azure AD-connector ruimte geïmporteerd. Om het toe te voegen aan de lijst met geïmporteerde kenmerken:

1. Selecteer het tabblad **connectors** in de Synchronization Service Manager.
2. Klik met de rechter muisknop op de Azure AD-connector en selecteer **Eigenschappen**.
3. Ga in het pop-upvenster naar het tabblad **kenmerken selecteren** .
4. Selecteer het kenmerk **preferredDataLocation** in de lijst.
5. Selecteer **OK** om op te slaan.

![Scherm afbeelding van het dialoog venster Synchronization Service Manager en eigenschappen](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Stap 5: een regel voor binnenkomende synchronisatie maken
Met de regel voor binnenkomende synchronisatie kan de kenmerk waarde worden gestroomd van het bron kenmerk in on-premises Active Directory naar de oorspronkelijke tekst.

1. Start de **Editor voor synchronisatie regels** door te **START** gaan met de  >  **Editor voor synchronisatie regels** starten.
2. Stel de **richting** van het zoek filter in op **binnenkomend**.
3. Selecteer **nieuwe regel toevoegen** om een nieuwe regel voor binnenkomende verbindingen te maken.
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | --- | --- | --- |
    | Name | *Geef een naam op* | Bijvoorbeeld ' in van AD: gebruiker preferredDataLocation ' |
    | Description | *Geef een aangepaste beschrijving op* |  |
    | Verbonden systeem | *Kies de on-premises Active Directory-connector* |  |
    | Type verbonden systeem object | **Gebruiker** |  |
    | Omgekeerd object type | **Person** |  |
    | Koppelings type | **Join** |  |
    | Prioriteit | *Kies een getal tussen 1 en 99* | 1 – 99 is gereserveerd voor aangepaste synchronisatie regels. Kies geen waarde die wordt gebruikt door een andere synchronisatie regel. |

5. Laat het **bereik filter** leeg, zodat alle objecten worden toegevoegd. Mogelijk moet u het bereik filter aanpassen op basis van uw Azure AD Connect-implementatie.
6. Ga naar het **tabblad trans formatie** en implementeer de volgende transformatie regel:

    | Stroom type | Doel kenmerk | Bron | Eenmaal Toep assen | Type samen voeging |
    | --- | --- | --- | --- | --- |
    |Direct | preferredDataLocation | Het bron kenmerk kiezen | Niet ingeschakeld | Bijwerken |

7. Als u de regel voor binnenkomend verkeer wilt maken, selecteert u **toevoegen**.

![Scherm opname van regel voor het maken van een inkomende synchronisatie](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Stap 6: een regel voor uitgaande synchronisatie maken
De regel voor uitgaande synchronisatie maakt het mogelijk dat de waarde van het kenmerk wordt overgelopen van de tekst in het kenmerk **preferredDataLocation** in azure AD:

1. Ga naar de **Editor voor synchronisatie regels**.
2. Stel de **richting** van het zoek filter in op **uitgaand**.
3. Selecteer **nieuwe regel toevoegen**.
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | ----- | ------ | --- |
    | Name | *Geef een naam op* | Bijvoorbeeld ' naar Azure AD: User preferredDataLocation ' |
    | Description | *Geef een beschrijving op* ||
    | Verbonden systeem | *De Azure AD-connector selecteren* ||
    | Type verbonden systeem object | **Gebruiker** ||
    | Omgekeerd object type | **Person** ||
    | Koppelings type | **Join** ||
    | Prioriteit | *Kies een getal tussen 1 en 99* | 1 – 99 is gereserveerd voor aangepaste synchronisatie regels. Kies geen waarde die wordt gebruikt door een andere synchronisatie regel. |

5. Ga naar het tabblad **filter bereik** en voeg één bereik filter groep met twee componenten toe:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | sourceObjectType | WAARD | Gebruiker |
    | cloudMastered | NOTEQUAL | True |

    Het bereik filter bepaalt op welke Azure AD-objecten deze regel voor uitgaande synchronisatie wordt toegepast. In dit voor beeld gebruiken we hetzelfde bereik filter van ' out to Azure AD: User Identity ' OOB (out-of-box)-synchronisatie regel. Hiermee wordt voor komen dat de synchronisatie regel wordt toegepast op **gebruikers** objecten die niet zijn gesynchroniseerd vanuit een on-premises Active Directory. Mogelijk moet u het bereik filter aanpassen op basis van uw Azure AD Connect-implementatie.

6. Ga naar het tabblad **trans formatie** en implementeer de volgende transformatie regel:

    | Stroom type | Doel kenmerk | Bron | Eenmaal Toep assen | Type samen voeging |
    | --- | --- | --- | --- | --- |
    | Direct | preferredDataLocation | preferredDataLocation | Niet ingeschakeld | Bijwerken |

7. Sluit **toevoegen** om de uitgaande regel te maken.

![Scherm opname van regel voor uitgaande synchronisatie maken](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Stap 7: volledige synchronisatie cyclus uitvoeren
In het algemeen is volledige synchronisatie cyclus vereist. Dit komt doordat u nieuwe kenmerken hebt toegevoegd aan het Active Directory-en Azure AD-connector schema en aangepaste synchronisatie regels hebt geïntroduceerd. Controleer de wijzigingen voordat u ze naar Azure AD exporteert. U kunt de volgende stappen gebruiken om de wijzigingen te verifiëren, terwijl u de stappen die een volledige synchronisatie cyclus vormen hand matig uitvoert.

1. **Volledige import** bewerking uitvoeren op de on-premises Active Directory-connector:

   1. Ga naar het tabblad **bewerkingen** in het Synchronization Service Manager.
   2. Klik met de rechter muisknop op de **on-premises Active Directory-Connector** en selecteer **uitvoeren**.
   3. Selecteer in het dialoog venster **volledig importeren** en selecteer **OK**.
   4. Wacht tot de bewerking is voltooid.

      > [!NOTE]
      > U kunt volledige import overs laan op de on-premises Active Directory-connector als het bron kenmerk al is opgenomen in de lijst met geïmporteerde kenmerken. Met andere woorden, u hebt geen wijziging aangebracht tijdens stap 2 eerder in dit artikel.

2. **Volledige import** bewerking uitvoeren op de Azure AD-connector:

   1. Klik met de rechter muisknop op de **Azure AD-connector** en selecteer **uitvoeren**.
   2. Selecteer in het dialoog venster **volledig importeren** en selecteer **OK**.
   3. Wacht tot de bewerking is voltooid.

3. Controleer de synchronisatie regel wijzigingen voor een bestaand **gebruikers** object.

   Het bron kenmerk van on-premises Active Directory en **preferredDataLocation** van Azure AD zijn geïmporteerd in elke respectieve connector ruimte. Voordat u doorgaat met de volledige synchronisatie stap, voert u een voor beeld uit van een bestaand **gebruikers** object in de on-premises Active Directory-Connector ruimte. Het bron kenmerk moet zijn ingevuld voor het object dat u hebt gekozen. Een geslaagde Preview met **preferredDataLocation** die in de tekst is ingevuld, is een goede indicatie dat u de synchronisatie regels correct hebt geconfigureerd. Zie [de wijziging controleren](how-to-connect-sync-change-the-configuration.md#verify-the-change)voor meer informatie over het uitvoeren van een preview-versie.

4. **Volledige synchronisatie** uitvoeren op de on-premises Active Directory connector:

   1. Klik met de rechter muisknop op de **on-premises Active Directory-Connector** en selecteer **uitvoeren**.
   2. Selecteer **volledige synchronisatie** in het dialoog venster en selecteer **OK**.
   3. Wacht tot de bewerking is voltooid.

5. Controleer **in afwachting van export** naar Azure AD:

   1. Klik met de rechter muisknop op de **Azure AD-connector** en selecteer **ruimte voor Zoek connector**.
   2. In het dialoog venster **Zoek connector ruimte** :

        a. Stel het **bereik** in op **in behandeling zijnde export**.<br>
        b. Schakel alle drie de selectie vakjes in, waaronder **toevoegen, wijzigen en verwijderen**.<br>
        c. Selecteer **zoeken** om de lijst met objecten te bekijken met de wijzigingen die moeten worden geëxporteerd. Als u de wijzigingen voor een bepaald object wilt onderzoeken, dubbelklikt u op het object.<br>
        d. Controleer of de wijzigingen worden verwacht.

6. **Export** uitvoeren op de **Azure AD-connector**

   1. Klik met de rechter muisknop op de **Azure AD-connector** en selecteer **uitvoeren**.
   2. Selecteer in het dialoog venster **Connector uitvoeren** de optie **exporteren** en selecteer **OK**.
   3. Wacht tot de bewerking is voltooid.

> [!NOTE]
> U zult merken dat de stappen niet de volledige synchronisatie stap voor de Azure AD-connector bevatten of de stap exporteren op de Active Directory-connector. De stappen zijn niet vereist, omdat de kenmerk waarden alleen vanuit on-premises Active Directory naar Azure AD stromen.

## <a name="step-8-re-enable-sync-scheduler"></a>Stap 8: de synchronisatie planner opnieuw inschakelen
De ingebouwde synchronisatie planner opnieuw inschakelen:

1. Start een Power shell-sessie.
2. Schakel geplande synchronisatie opnieuw in door deze cmdlet uit te voeren: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Stap 9: het resultaat controleren
Het is nu tijd om de configuratie te controleren en in te scha kelen voor uw gebruikers.

1. Voeg de geo toe aan het geselecteerde kenmerk van een gebruiker. De lijst met beschik bare geografische gebieden kan worden gevonden in deze tabel.  
![Scherm opname van AD-kenmerk toegevoegd aan een gebruiker](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Wacht totdat het kenmerk is gesynchroniseerd met Azure AD.
3. Controleer met behulp van Exchange Online Power shell of de Postvak regio juist is ingesteld.  
![Scherm afbeelding van Exchange Online Power shell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Ervan uitgaande dat uw Tenant is gemarkeerd om deze functie te kunnen gebruiken, wordt het postvak verplaatst naar de juiste geo-waarde. U kunt dit controleren door te kijken naar de naam van de server waarop het postvak zich bevindt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over multi-geo in Microsoft 365:

* [Multi-geo-sessies op Ignite](https://aka.ms/MultiGeoIgnite)
* [Meerdere geografische locaties in OneDrive](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [Meerdere geografische locaties in share point online](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

Meer informatie over het configuratie model in de sync-engine:

* Meer informatie over het configuratie model vindt u in [informatie over declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Meer informatie over de expressie taal vindt u in [uitleg over declaratieve inrichtings expressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Overzichts onderwerpen:

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)