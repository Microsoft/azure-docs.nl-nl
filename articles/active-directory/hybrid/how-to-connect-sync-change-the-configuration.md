---
title: 'Azure AD Connect synchronisatie: een wijziging in de standaard configuratie aanbrengen'
description: Begeleidt u bij het maken van een wijziging in de configuratie in Azure AD Connect Sync.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2044653673da10de59d5ff125da44ac1f89e22f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861847"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect synchronisatie: een wijziging in de standaard configuratie aanbrengen
Het doel van dit artikel is om u te laten zien hoe u wijzigingen aanbrengt in de standaard configuratie in Azure Active Directory (Azure AD) Connect Sync. Het bevat stappen voor enkele veelvoorkomende scenario's. Met deze kennis moet u eenvoudige wijzigingen in uw eigen configuratie kunnen aanbrengen op basis van uw eigen bedrijfs regels.

> [!WARNING]
> Als u de standaard regels voor de out-of-Box-synchronisatie wijzigt, worden deze wijzigingen overschreven wanneer de volgende keer Azure AD Connect wordt bijgewerkt, wat resulteert in onverwachte en waarschijnlijk ongewenste synchronisatie resultaten.
>
> De standaard regels voor out-of-Box Sync hebben een vinger afdruk. Als u deze regels wijzigt, komt de vinger afdruk niet meer overeen. Het kan voor komen dat u in de toekomst problemen ondervindt wanneer u een nieuwe versie van Azure AD Connect probeert toe te passen. Breng alleen wijzigingen aan zoals beschreven in dit artikel.

## <a name="synchronization-rules-editor"></a>Editor voor synchronisatie regels
De editor voor synchronisatie regels wordt gebruikt om de standaard configuratie te bekijken en te wijzigen. U vindt deze in het menu **Start** onder de groep **Azure AD Connect** .  
![Menu Start met synchronisatie regel Editor](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Wanneer u de editor opent, ziet u de standaard regels voor out-of-Box.

![Editor voor synchronisatie regels](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigeren in de editor
Met behulp van de vervolg keuzelijsten aan de bovenkant van de editor kunt u snel een specifieke regel vinden. Als u bijvoorbeeld de regels wilt zien waarin het kenmerk proxyAddresses is opgenomen, kunt u de vervolg keuzelijsten als volgt wijzigen:  
![SRE filteren](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Druk op F5 op het toetsen bord om het filteren opnieuw in te stellen en een nieuwe configuratie te laden.

Rechtsboven is de knop **nieuwe regel toevoegen** . U gebruikt deze knop om uw eigen aangepaste regel te maken.

Onderaan staan knoppen voor het handelen op een geselecteerde synchronisatie regel. **Bewerk** en **Verwijder** wat u verwacht. **Exporteren** produceert een Power shell-script voor het opnieuw maken van de synchronisatie regel. Met deze procedure kunt u een synchronisatie regel verplaatsen van de ene server naar een andere.

## <a name="create-your-first-custom-rule"></a>Uw eerste aangepaste regel maken
De meest voorkomende wijzigingen zijn de kenmerk stromen. De gegevens in de bron directory zijn mogelijk niet hetzelfde als in azure AD. In het voor beeld in deze sectie moet u ervoor zorgen dat de opgegeven naam van een gebruiker *altijd in orde* is.

### <a name="disable-the-scheduler"></a>De scheduler uitschakelen
De [scheduler](how-to-connect-sync-feature-scheduler.md) wordt standaard elke 30 minuten uitgevoerd. Zorg ervoor dat deze niet wordt gestart tijdens het maken van wijzigingen en het oplossen van problemen met uw nieuwe regels. Als u de scheduler tijdelijk wilt uitschakelen, start u Power shell en voert u uit `Set-ADSyncScheduler -SyncCycleEnabled $false` .

![De scheduler uitschakelen](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>De regel maken
1. Klik op **nieuwe regel toevoegen**.
2. Voer op de pagina **Beschrijving** het volgende in:  
   ![Binnenkomende regel filtering](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Naam**: Geef de regel een beschrijvende naam.
   * **Beschrijving**: Geef een uitleg zodat iemand anders kan begrijpen waarvoor de regel geldt.
   * **Verbonden systeem**: dit is het systeem waarin het object kan worden gevonden. Selecteer in dit geval **Active Directory-Connector**.
   * **Verbonden systeem/omgekeerd object type**: Selecteer respectievelijk **gebruiker** en **persoon**.
   * **Koppelings type**: Wijzig deze waarde om **samen te voegen**.
   * **Prioriteit**: Geef een waarde op die uniek is in het systeem. Een lagere numerieke waarde duidt op een hogere prioriteit.
   * **Tag**: laat dit leeg. Alleen out-of-Box-regels van micro soft moeten in dit vak zijn ingevuld met een waarde.
3. Voer op de pagina **bereik filteren** de **opgegeven naam ISNOTNULL** in.  
   ![Bereik van binnenkomende regel filter](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Deze sectie wordt gebruikt om te definiëren op welke objecten de regel moet worden toegepast. Als deze leeg is, is de regel van toepassing op alle gebruikers objecten. Dit omvat echter wel Vergader ruimten, service accounts en andere gebruikers objecten die geen persoon zijn.
4. Laat het veld leeg op de pagina **regels voor samen voegen** .
5. Wijzig op de pagina **trans formaties** **FlowType** in **Expression**. Selecteer voor **doel kenmerk** **opgegeven** naam. Voer **PCase ([OpgegevenNaam])** in bij **bron**.
   ![Trans formaties van binnenkomende regel](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   De synchronisatie-engine is hoofdletter gevoelig voor zowel de functie naam als de naam van het kenmerk. Als u iets verkeerd typt, wordt er een waarschuwing weer gegeven wanneer u de regel toevoegt. U kunt opslaan en door gaan, maar u moet de regel opnieuw openen en corrigeren.
6. Klik op **toevoegen** om de regel op te slaan.

De nieuwe aangepaste regel moet zichtbaar zijn met de andere synchronisatie regels in het systeem.

### <a name="verify-the-change"></a>De wijziging verifiëren
Met deze nieuwe wijziging wilt u controleren of deze werkt zoals verwacht en dat er geen fouten optreden. Afhankelijk van het aantal objecten dat u hebt, zijn er twee manieren om deze stap uit te voeren:

- Voer een volledige synchronisatie uit op alle objecten.
- Een preview-versie en volledige synchronisatie uitvoeren op één object.

Open de **synchronisatie service** vanuit het menu **Start** . De stappen in deze sectie zijn allemaal in dit hulp programma.

**Volledige synchronisatie op alle objecten**  

   1. Selecteer de **connectors** bovenaan. Identificeer de connector die u in de vorige sectie hebt gewijzigd (in dit geval Active Directory Domain Services) en selecteer deze. 
   2. Selecteer **uitvoeren** bij **acties**.
   3. Selecteer **volledige synchronisatie** en selecteer vervolgens **OK**.
   ![Volledige synchronisatie](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   De objecten worden nu bijgewerkt in het omgekeerde. Controleer uw wijzigingen door te kijken naar het object in de tekst.

**Voor beeld en volledige synchronisatie van een enkel object**  

   1. Selecteer de **connectors** bovenaan. Identificeer de connector die u in de vorige sectie hebt gewijzigd (in dit geval Active Directory Domain Services) en selecteer deze.
   2. Selecteer **ruimte Zoek connector**. 
   3. Gebruik **bereik** om een object te vinden dat u wilt gebruiken om de wijziging te testen. Selecteer het object en klik op **voor beeld**. 
   4. Selecteer **Doorvoervoorbeeld** op het nieuwe scherm.  
   ![Voor beeld van vastleggen](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   De wijziging wordt nu doorgevoerd in de omgekeerde.

**Het object in de omgekeerde tekst weer geven**  

1. Kies een paar voorbeeld objecten om ervoor te zorgen dat de waarde wordt verwacht en dat de regel wordt toegepast. 
2. Selecteer de optie voor **omgekeerde zoek opdracht** van de bovenkant. Voeg een filter toe dat u nodig hebt om de relevante objecten te vinden. 
3. Open een object vanuit het Zoek resultaat. Bekijk de kenmerk waarden en Controleer ook in de kolom **synchronisatie regels** dat de regel wordt toegepast zoals verwacht.  
![Metaverse zoeken](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>De Scheduler inschakelen
Als alles zoals verwacht, kunt u de Scheduler opnieuw inschakelen. Voer uit in Power shell `Set-ADSyncScheduler -SyncCycleEnabled $true` .

## <a name="other-common-attribute-flow-changes"></a>Andere veelvoorkomende kenmerk stroom wijzigingen
In de vorige sectie is beschreven hoe u wijzigingen aanbrengt in een kenmerk stroom. In deze sectie worden enkele aanvullende voor beelden gegeven. De stappen voor het maken van de synchronisatie regel worden afgekort, maar u kunt de volledige stappen in de vorige sectie vinden.

### <a name="use-an-attribute-other-than-the-default"></a>Een ander kenmerk dan de standaard waarde gebruiken
In dit scenario van Fabrikam is er een forest waarin het lokale alfabet wordt gebruikt voor de gegeven naam, achternummer en weergave naam. De Latijnse teken weergave van deze kenmerken vindt u in de extensie kenmerken. Voor het bouwen van een algemene adres lijst in azure AD en Microsoft 365, wil de organisatie deze kenmerken gebruiken.

Met een standaard configuratie ziet een object uit het lokale forest er als volgt uit:  
![Kenmerk stroom 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Ga als volgt te werk om een regel met andere kenmerk stromen te maken:

1. Open de **Editor voor synchronisatie regels** vanuit het menu **Start** .
2. Klik op de knop **nieuwe regel toevoegen** terwijl **Inkomend** nog is geselecteerd aan de linkerkant.
3. Geef een naam en beschrijving voor de regel op. Selecteer de on-premises Active Directory instantie en de relevante object typen. Selecteer in **type koppeling** de optie **samen voegen**. Kies voor **rang** een nummer dat niet wordt gebruikt door een andere regel. De out-of-Box-regels beginnen met 100, dus de waarde 50 kan in dit voor beeld worden gebruikt.
  ![Kenmerk stroom 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Het **bereik filter** leeg laten. (Dat wil zeggen, het moet worden toegepast op alle gebruikers objecten in het forest.)
5. Laat de **regels voor samen voegen** leeg. (Dat wil zeggen dat de out-of-Box-regel alle samen voegingen moet afhandelen.)
6. Maak in **trans formaties** de volgende stromen:  
  ![Kenmerk stroom 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Klik op **toevoegen** om de regel op te slaan.
8. Ga naar **Synchronization Service Manager**. Selecteer op **connectors** de connector waaraan u de regel hebt toegevoegd. Selecteer **uitvoeren** en selecteer **volledige synchronisatie**. Bij een volledige synchronisatie worden alle objecten opnieuw berekend met behulp van de huidige regels.

Dit is het resultaat voor hetzelfde object met deze aangepaste regel:  
![Kenmerk stroom 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Lengte van kenmerken
Teken reeks kenmerken zijn standaard geïndexeerd en de maximale lengte is 448 tekens. Als u werkt met teken reeks kenmerken die meer kunnen bevatten, moet u ervoor zorgen dat u het volgende opneemt in de kenmerk stroom:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>De userPrincipalSuffix wijzigen
Het kenmerk userPrincipalName in Active Directory wordt niet altijd door de gebruikers herkend en is mogelijk niet geschikt als de aanmeldings-ID. Met de wizard voor het installeren van Azure AD Connect kunt u een ander kenmerk kiezen, bijvoorbeeld *mail*. Maar in sommige gevallen moet het kenmerk worden berekend.

Het bedrijf Contoso heeft bijvoorbeeld twee Azure AD-directory's, één voor productie en één voor het testen. Ze willen dat gebruikers in hun test Tenant een ander achtervoegsel gebruiken in de aanmeldings-ID:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

In deze expressie neemt alles links van het eerste @-sign (woord) en samen voegen met een vaste teken reeks.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Een kenmerk met meerdere waarden converteren naar een enkele waarde
Sommige kenmerken in Active Directory zijn meerdere waarden in het schema, zelfs als ze in Active Directory gebruikers en computers met één waarde worden weer geven. Een voor beeld is het kenmerk Beschrijving:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Als in deze expressie het kenmerk een waarde heeft, neemt u het eerste item (*item*) in het kenmerk op, verwijdert u voor loop-en volg spaties (*Trim*) en behoudt u de eerste 448 tekens (*links*) in de teken reeks.

### <a name="do-not-flow-an-attribute"></a>Geen kenmerk stroomt
Zie [het kenmerk flow-proces beheren](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process)voor achtergrond informatie over het scenario voor deze sectie.

Er zijn twee manieren om een kenmerk niet te stroom te maken. De eerste is met behulp van de installatie wizard om [geselecteerde kenmerken te verwijderen](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Deze optie werkt als u het kenmerk nog nooit eerder hebt gesynchroniseerd. Als u echter bent begonnen met het synchroniseren van dit kenmerk en het later met deze functie verwijdert, stopt de synchronisatie-engine met het beheren van het kenmerk en blijven de bestaande waarden in azure AD.

Als u de waarde van een kenmerk wilt verwijderen en er zeker van wilt zijn dat deze niet meer in de toekomst stroomt, moet u een aangepaste regel maken.

In dit scenario van Fabrikam hebben we gerealiseerd dat sommige kenmerken die worden gesynchroniseerd met de Cloud, niet zijn. We willen er zeker van zijn dat deze kenmerken worden verwijderd uit Azure AD.  
![Ongeldige extensie kenmerken](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Maak een nieuwe regel voor binnenkomende synchronisatie en vul de beschrijving in.
  ![Beschreven](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Maak kenmerk stromen met de **expressie** voor **FlowType** en met **AuthoritativeNull** voor de **bron**. De letterlijke **AuthoritativeNull** geeft aan dat de waarde leeg moet zijn in het omgekeerde, zelfs als een regel voor een synchronisatie met een lagere prioriteit probeert de waarde in te vullen.
  ![Trans formatie voor extensie kenmerken](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Sla de synchronisatie regel op. Start de **synchronisatie service**, zoek de connector, selecteer **uitvoeren** en selecteer **volledige synchronisatie**. Met deze stap worden alle kenmerk stromen opnieuw berekend.
4. Controleer of de bedoelde wijzigingen worden geëxporteerd door de connector ruimte te doorzoeken.
  ![Gefaseerd verwijderen](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Regels maken met Power shell
Het gebruik van de editor voor synchronisatie regels werkt prima wanneer u slechts enkele wijzigingen hebt aangebracht. Als u veel wijzigingen wilt aanbrengen, is Power shell mogelijk een betere optie. Sommige geavanceerde functies zijn alleen beschikbaar met Power shell.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Het Power shell-script voor een out-of-Box-regel ophalen
Als u het Power shell-script wilt zien waarin een out-of-Box-regel is gemaakt, selecteert u de regel in de editor voor synchronisatie regels en klikt u op **exporteren**. Deze actie geeft u het Power shell-script waarmee de regel is gemaakt.

### <a name="advanced-precedence"></a>Geavanceerde prioriteit
De out-of-Box Sync-regels beginnen met een prioriteits waarde van 100. Als u veel forests hebt en u veel aangepaste wijzigingen moet aanbrengen, zijn de 99-synchronisatie regels mogelijk niet voldoende.

U kunt de synchronisatie-engine instrueren dat u aanvullende regels wilt invoegen vóór de out-of-Box-regels. Voer de volgende stappen uit om dit gedrag te verkrijgen:

1. Markeer de eerste out-of-Box-synchronisatie regel (**in vanuit AD-User-koppeling**) in de editor voor synchronisatie regels en selecteer **exporteren**. Kopieer de waarde voor de SR-id.  
![Power shell vóór wijziging](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Maak de nieuwe synchronisatie regel. U kunt de editor voor synchronisatie regels gebruiken om deze te maken. Exporteer de regel naar een Power shell-script.
3. In de eigenschap **PrecedenceBefore** voert u de id-waarde in van de out-of-Box-regel. Stel de **prioriteit** in op **0**. Zorg ervoor dat het kenmerk Identifier uniek is en dat u geen GUID van een andere regel opnieuw gebruikt. Zorg er ook voor dat de eigenschap **ImmutableTag** niet is ingesteld. Deze eigenschap moet alleen worden ingesteld voor een out-of-Box-regel.
4. Sla het Power shell-script op en voer dit uit. Het resultaat is dat aan uw aangepaste regel de prioriteits waarde 100 wordt toegewezen en dat alle andere regels voor out-of-Box worden verhoogd.  
![Power shell na wijziging](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

U kunt veel aangepaste synchronisatie regels hebben door dezelfde **PrecedenceBefore** -waarde te gebruiken wanneer dat nodig is.

## <a name="enable-synchronization-of-usertype"></a>Synchronisatie van User type inschakelen
Azure AD Connect ondersteunt synchronisatie van het kenmerk **User type** voor **gebruikers** objecten in versie 1.1.524.0 en hoger. Meer specifiek zijn de volgende wijzigingen geïntroduceerd:

- Het schema van de **gebruiker** van het object type in de Azure AD-connector wordt uitgebreid met het kenmerk User type, dat van het type teken reeks is en is voorzien van een enkele waarde.
- Het schema van de **persoon** van het object type in de tekst wordt uitgebreid met het kenmerk User type, dat van het type teken reeks is en is voorzien van een enkele waarde.

Het kenmerk User type is standaard niet ingeschakeld voor synchronisatie omdat er geen overeenkomend User type-kenmerk aanwezig is in on-premises Active Directory. U moet de synchronisatie hand matig inschakelen. Voordat u dit doet, moet u rekening houden met het volgende gedrag dat wordt afgedwongen door Azure AD:

- Azure AD accepteert alleen twee waarden voor het kenmerk User type: **lid** en **gast**.
- Als het kenmerk User type niet is ingeschakeld voor synchronisatie in Azure AD Connect, zou de Azure AD-gebruikers die zijn gemaakt via Directory-synchronisatie, het kenmerk User type hebben ingesteld op **lid**.
- Vóór versie 1.5.30.0 heeft Azure AD het kenmerk User type op bestaande Azure AD-gebruikers niet toegestaan door Azure AD Connect. In oudere versies kan deze alleen worden ingesteld tijdens het maken van de Azure AD-gebruikers en [gewijzigd via Power shell](/powershell/module/azuread/set-azureaduser).

Voordat u synchronisatie van het kenmerk User type inschakelt, moet u eerst bepalen hoe het kenmerk wordt afgeleid van on-premises Active Directory. Hier volgen de meest voorkomende benaderingen:

- Wijs een ongebruikt on-premises AD-kenmerk (zoals extensionAttribute1) aan dat moet worden gebruikt als het bron kenmerk. Het toegewezen on-premises AD-kenmerk moet van het type **teken reeks** zijn, moet een enkele waarde hebben en het lid of het **dimensielid** bevatten.  

    Als u deze aanpak kiest, moet u ervoor zorgen dat het aangewezen kenmerk is gevuld met de juiste waarde voor alle bestaande gebruikers objecten in on-premises Active Directory die zijn gesynchroniseerd met Azure AD voordat u synchronisatie van het kenmerk User type inschakelt.

- U kunt ook de waarde voor het kenmerk User type afleiden uit andere eigenschappen. U wilt bijvoorbeeld alle gebruikers synchroniseren als **gast** als hun on-premises AD userPrincipalName-kenmerk eindigt met het domein onderdeel <em>@partners.fabrikam123.org</em> . 

    Zoals eerder vermeld, staat oudere versies van Azure AD Connect niet toe dat het kenmerk User type van bestaande Azure AD-gebruikers door Azure AD Connect wordt gewijzigd. Daarom moet u ervoor zorgen dat de logica die u hebt besloten, consistent is met de manier waarop het kenmerk User type al is geconfigureerd voor alle bestaande Azure AD-gebruikers in uw Tenant.

De stappen voor het inschakelen van de synchronisatie van het kenmerk User type kunnen als volgt worden samenvatten:

1.  Schakel de synchronisatie planner uit en controleer of er geen synchronisatie wordt uitgevoerd.
2.  Voeg het bron kenmerk toe aan het on-premises AD connector-schema.
3.  Voeg de User type toe aan het Azure AD-connector schema.
4.  Maak een regel voor binnenkomende synchronisatie om de kenmerk waarde van on-premises Active Directory uit te stromen.
5.  Maak een regel voor uitgaande synchronisatie om de kenmerk waarde naar Azure AD te stromen.
6.  Voer een volledige synchronisatie cyclus uit.
7.  Schakel de synchronisatie planner in.

>[!NOTE]
> In de rest van deze sectie wordt beschreven hoe u deze stappen uitvoert. Deze worden beschreven in de context van een Azure AD-implementatie met een topologie met één forest en zonder aangepaste synchronisatie regels. Als u een topologie met meerdere forests hebt, aangepaste synchronisatie regels hebt geconfigureerd of een staging-server hebt, moet u de stappen dienovereenkomstig aanpassen.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Stap 1: de synchronisatie planner uitschakelen en controleren of er geen synchronisatie wordt uitgevoerd
Om te voor komen dat u onbedoelde wijzigingen naar Azure AD exporteert, moet u ervoor zorgen dat er geen synchronisatie plaatsvindt terwijl u de synchronisatie regels bijwerkt. De ingebouwde synchronisatie planner uitschakelen:

 1. Start een Power shell-sessie op de Azure AD Connect-server.
 2. Schakel de geplande synchronisatie uit door de cmdlet uit te voeren `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 3. Open de Synchronization Service Manager door de   >  **synchronisatie service** te starten.
 4. Ga naar het tabblad **bewerkingen** en controleer of er geen bewerking is met de status wordt *uitgevoerd*.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Stap 2: het bron kenmerk toevoegen aan het on-premises AD-connector schema
Niet alle Azure AD-kenmerken worden geïmporteerd in de on-premises AD connector-ruimte. Om het bron kenmerk toe te voegen aan de lijst met geïmporteerde kenmerken:

 1. Ga naar het tabblad **connectors** in de Synchronization Service Manager.
 2. Klik met de rechter muisknop op de on-premises AD-connector en selecteer **Eigenschappen**.
 3. Ga in het pop-upvenster naar het tabblad **kenmerken selecteren** .
 4. Zorg ervoor dat het bron kenmerk is geselecteerd in de lijst met kenmerken.
 5. Klik op **OK** om op te slaan.
![Bron kenmerk toevoegen aan on-premises AD connector-schema](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>Stap 3: het kenmerk User type toevoegen aan het Azure AD-connector schema
Het kenmerk User type wordt standaard niet geïmporteerd in de Azure AD Connect ruimte. Het kenmerk User type toevoegen aan de lijst met geïmporteerde kenmerken:

 1. Ga naar het tabblad **connectors** in de Synchronization Service Manager.
 2. Klik met de rechter muisknop op de **Azure AD-connector** en selecteer **Eigenschappen**.
 3. Ga in het pop-upvenster naar het tabblad **kenmerken selecteren** .
 4. Zorg ervoor dat het kenmerk User type is geselecteerd in de lijst met kenmerken.
 5. Klik op **OK** om op te slaan.

![Bron kenmerk toevoegen aan Azure AD-connector schema](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Stap 4: een regel voor binnenkomende synchronisatie maken om de kenmerk waarde van on-premises Active Directory uit te stromen
De regel voor binnenkomende synchronisatie maakt het mogelijk dat de kenmerk waarde van het bron kenmerk van on-premises Active Directory naar de omgekeerde tekst kan stromen:

1. Open de editor voor synchronisatie regels door te gaan naar de  >  **Editor voor synchronisatie regels** starten.
2. Stel de **richting** van het zoek filter in op **binnenkomend**.
3. Klik op de knop **nieuwe regel toevoegen** om een nieuwe regel voor binnenkomende verbindingen te maken.
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | --- | --- | --- |
    | Name | *Geef een naam op* | Bijvoorbeeld *in van AD: gebruiker user type* |
    | Beschrijving | *Geef een beschrijving op* |  |
    | Verbonden systeem | *Kies de on-premises AD-connector* |  |
    | Type verbonden systeem object | **Gebruiker** |  |
    | Omgekeerd object type | **Person** |  |
    | Koppelings type | **Join** |  |
    | Prioriteit | *Kies een getal tussen 1 en 99* | 1 – 99 is gereserveerd voor aangepaste synchronisatie regels. Kies geen waarde die wordt gebruikt door een andere synchronisatie regel. |

5. Ga naar het tabblad **filter bereik** en voeg **één bereik filter groep** toe met de volgende component:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Gebruiker\_ |

    Het filter bereik bepaalt op welke on-premises AD-objecten deze regel voor binnenkomende synchronisatie wordt toegepast. In dit voor beeld gebruiken we hetzelfde bereik filter dat wordt gebruikt in de regel *van van AD: User common* out-of-Box Synchronization, waarmee wordt voor komen dat de synchronisatie regel wordt toegepast op gebruikers objecten die zijn gemaakt met de functie voor het terugschrijven van gebruikers van Azure AD. Mogelijk moet u het bereik filter aanpassen op basis van uw Azure AD Connect-implementatie.

6. Ga naar het tabblad **trans formatie** en implementeer de gewenste transformatie regel. Als u bijvoorbeeld een ongebruikt on-premises AD-kenmerk (zoals extensionAttribute1) hebt aangewezen als bron kenmerk voor de User type, kunt u een directe kenmerk stroom implementeren:

    | Stroom type | Doel kenmerk | Bron | Eenmaal Toep assen | Type samen voeging |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | extensionAttribute1 | Niet ingeschakeld | Bijwerken |

    In een ander voor beeld wilt u de waarde voor het kenmerk User type afleiden van andere eigenschappen. U wilt bijvoorbeeld alle gebruikers synchroniseren als gast als hun on-premises AD userPrincipalName-kenmerk eindigt met het domein onderdeel <em>@partners.fabrikam123.org</em> . U kunt als volgt een expressie implementeren:

    | Stroom type | Doel kenmerk | Bron | Eenmaal Toep assen | Type samen voeging |
    | --- | --- | --- | --- | --- |
    | Expression | UserType | IIF (IsPresent ([userPrincipalName]), IIF (CBool (/gebruiker) (LCase ([userPrincipalName]), " @partners.fabrikam123.org ") = 0), "lid", "gast"), fout ("userPrincipalName is niet aanwezig om User type te bepalen")) | Niet ingeschakeld | Bijwerken |

7. Klik op **toevoegen** om de regel voor binnenkomend verkeer te maken.

![Regel voor binnenkomende synchronisatie maken](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Stap 5: een regel voor uitgaande synchronisatie maken om de kenmerk waarde naar Azure AD te stromen
De regel voor uitgaande synchronisatie maakt het mogelijk dat de waarde van het kenmerk wordt overgelopen van de tekst in het kenmerk User type in azure AD:

1. Ga naar de editor voor synchronisatie regels.
2. Stel de **richting** van het zoek filter in op **uitgaand**.
3. Klik op de knop **nieuwe regel toevoegen** .
4. Geef onder het tabblad **Beschrijving** de volgende configuratie op:

    | Kenmerk | Waarde | Details |
    | ----- | ------ | --- |
    | Name | *Geef een naam op* | Bijvoorbeeld voor *Aad: gebruiker user type* |
    | Beschrijving | *Geef een beschrijving op* ||
    | Verbonden systeem | *De AAD-connector selecteren* ||
    | Type verbonden systeem object | **Gebruiker** ||
    | Omgekeerd object type | **Person** ||
    | Koppelings type | **Join** ||
    | Prioriteit | *Kies een getal tussen 1 en 99* | 1 – 99 is gereserveerd voor aangepaste synchronisatie regels. Kies geen waarde die wordt gebruikt door een andere synchronisatie regel. |

5. Ga naar het tabblad **filter bereik** en voeg **één bereik filter groep** met twee componenten toe:

    | Kenmerk | Operator | Waarde |
    | --- | --- | --- |
    | sourceObjectType | WAARD | Gebruiker |
    | cloudMastered | NOTEQUAL | Waar |

    Het bereik filter bepaalt op welke Azure AD-objecten deze regel voor uitgaande synchronisatie wordt toegepast. In dit voor beeld gebruiken we dezelfde bereik filter van de out-of-Box-synchronisatie regel *out to AD* . Hiermee wordt voor komen dat de synchronisatie regel wordt toegepast op gebruikers objecten die niet zijn gesynchroniseerd vanuit on-premises Active Directory. Mogelijk moet u het bereik filter aanpassen op basis van uw Azure AD Connect-implementatie.

6. Ga naar het tabblad **trans formatie** en implementeer de volgende transformatie regel:

    | Stroom type | Doel kenmerk | Bron | Eenmaal Toep assen | Type samen voeging |
    | --- | --- | --- | --- | --- |
    | Direct | UserType | UserType | Niet ingeschakeld | Bijwerken |

7. Klik op **toevoegen** om de uitgaande regel te maken.

![Regel voor uitgaande synchronisatie maken](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Stap 6: een volledige synchronisatie cyclus uitvoeren
In het algemeen is een volledige synchronisatie cyclus vereist omdat we nieuwe kenmerken hebben toegevoegd aan de Active Directory-en Azure AD-connector schema's en aangepaste synchronisatie regels hebben geïntroduceerd. U wilt de wijzigingen controleren voordat u ze naar Azure AD exporteert. 

U kunt de volgende stappen gebruiken om de wijzigingen te controleren terwijl u de stappen voor een volledige synchronisatie cyclus hand matig uitvoert.

1. Een **volledige import** bewerking uitvoeren op de **on-premises AD-connector**:

   1. Ga naar het tabblad **connectors** in de Synchronization Service Manager.
   2. Klik met de rechter muisknop op de **on-premises AD-connector** en selecteer **uitvoeren**.
   3. Selecteer in het pop-updialoogvenster **volledige import** en klik vervolgens op **OK**.
   4. Wacht tot de bewerking is voltooid.

      > [!NOTE]
      > U kunt een volledige import bewerking overs laan voor de on-premises AD-connector als het bron kenmerk al is opgenomen in de lijst met geïmporteerde kenmerken. Met andere woorden, u hoeft geen wijzigingen aan te brengen tijdens [stap 2: Voeg het bron kenmerk toe aan het on-premises AD-connector schema](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema).

2. Een **volledige import** bewerking uitvoeren op de **Azure AD-connector**:

   1. Klik met de rechter muisknop op de **Azure AD-connector** en selecteer **uitvoeren**.
   2. Selecteer in het pop-updialoogvenster **volledige import** en klik vervolgens op **OK**.
   3. Wacht tot de bewerking is voltooid.

3. Controleer de synchronisatie regel wijzigingen voor een bestaand gebruikers object:

    Het bron kenmerk van on-premises Active Directory en de User type van Azure AD zijn geïmporteerd in hun respectieve connector ruimten. Voordat u doorgaat met een volledige synchronisatie, doet u een **voor beeld** van een bestaand gebruikers object in de on-premises AD connector-ruimte. Het bron kenmerk moet zijn ingevuld voor het object dat u hebt gekozen.
    
    Een geslaagde **Preview** met de User type die in de tekst is ingevuld, is een goede indicatie dat u de synchronisatie regels correct hebt geconfigureerd. Raadpleeg de sectie [de wijziging controleren](#verify-the-change)voor meer informatie over het uitvoeren van een **Preview-versie**.

4. Een **volledige synchronisatie** uitvoeren op de **on-premises AD-connector**:

   1. Klik met de rechter muisknop op de **on-premises AD-connector** en selecteer **uitvoeren**.
   2. Selecteer in het pop-updialoogvenster **volledige synchronisatie** en klik vervolgens op **OK**.
   3. Wacht tot de bewerking is voltooid.

5. Controleer **in afwachting van export** naar Azure AD:

   1. Klik met de rechter muisknop op de **Azure AD-connector** en selecteer **ruimte voor Zoek connector**.
   2. In het pop-upvenster **ruimte zoeken in Zoek connector** :

      - Stel het **bereik** in op **in behandeling zijnde export**.
      - Schakel alle drie de selectie vakjes in: **toevoegen**, **wijzigen** en **verwijderen**.
      - Klik op de knop **zoeken** om de lijst met objecten te verkrijgen met de wijzigingen die moeten worden geëxporteerd. Als u de wijzigingen voor een bepaald object wilt onderzoeken, dubbelklikt u op het object.
      - Controleer of de wijzigingen worden verwacht.

6. **Export** uitvoeren op de **Azure AD-connector**:

   1. Klik met de rechter muisknop op de **Azure AD-connector** en selecteer **uitvoeren**.
   2. Selecteer in het pop-upvenster **Connector uitvoeren** de optie **exporteren** en klik vervolgens op **OK**.
   3. Wacht totdat de export naar Azure AD is voltooid.

> [!NOTE]
> Deze stappen omvatten niet de volledige synchronisatie-en export stappen voor de Azure AD-connector. Deze stappen zijn niet vereist omdat de kenmerk waarden alleen vanuit on-premises Active Directory naar Azure AD stromen.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Stap 7: de synchronisatie planner opnieuw inschakelen
De ingebouwde synchronisatie planner opnieuw inschakelen:

1. Start een Power shell-sessie.
2. Schakel geplande synchronisatie opnieuw in door de cmdlet uit te voeren `Set-ADSyncScheduler -SyncCycleEnabled $true` .


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het configuratie model vindt u in [informatie over declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Meer informatie over de expressie taal vindt u in [uitleg over declaratieve inrichtings expressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Overzichts onderwerpen**

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
