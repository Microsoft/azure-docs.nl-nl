---
title: 'Azure AD Connect synchronisatie: filteren configureren | Microsoft Docs'
description: Hierin wordt uitgelegd hoe u filters configureert in Azure AD Connect synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996539"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect-synchronisatie: filtering configureren
Met behulp van filteren kunt u bepalen welke objecten in Azure Active Directory (Azure AD) uit uw on-premises map worden weer gegeven. Bij de standaard configuratie worden alle objecten in alle domeinen in de geconfigureerde forests gebruikt. In het algemeen is dit de aanbevolen configuratie. Gebruikers die Microsoft 365 werk belastingen, zoals Exchange Online en Skype voor bedrijven, profiteren van een volledige algemene adres lijst zodat ze een e-mail kunnen verzenden en iedereen kan bellen. Met de standaard configuratie zouden ze dezelfde ervaring hebben als bij een on-premises implementatie van Exchange of Lync.

In sommige gevallen moet u echter enkele wijzigingen aanbrengen in de standaard configuratie. Hier volgen enkele voorbeelden:

* U wilt de [multi-Azure AD-adreslijst topologie](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant)gebruiken. Vervolgens moet u een filter Toep assen om te bepalen welke objecten worden gesynchroniseerd met een bepaalde Azure AD-adres lijst.
* U voert een pilot uit voor Azure of Microsoft 365 en u wilt alleen een subset van gebruikers in azure AD. In de kleine pilot is het niet belang rijk dat u beschikt over een volledige algemene adres lijst om de functionaliteit te demonstreren.
* U hebt veel service accounts en andere niet-persoonlijke accounts die u niet wilt in azure AD.
* Om redenen van naleving verwijdert u geen gebruikers accounts on-premises. U schakelt ze alleen uit. In azure AD wilt u echter alleen actieve accounts weer geven.

In dit artikel wordt beschreven hoe u de verschillende filter methoden kunt configureren.

> [!IMPORTANT]
> Microsoft biedt geen ondersteuning voor het wijzigen of uitvoeren van Azure AD Connect-synchronisatie anders dan op de manier die officieel is gedocumenteerd. Een van deze acties kan leiden tot een inconsistente of niet-ondersteunde status van Azure AD Connect synchronisatie. Als gevolg hiervan kan micro soft geen technische ondersteuning bieden voor dergelijke implementaties.

## <a name="basics-and-important-notes"></a>Basis beginselen en belang rijke opmerkingen
In Azure AD Connect synchronisatie kunt u op elk gewenst moment filteren inschakelen. Als u begint met een standaard configuratie van Directory synchronisatie en vervolgens filteren configureert, worden de gefilterde objecten niet meer gesynchroniseerd met Azure AD. Als gevolg van deze wijziging, worden alle objecten in azure AD die eerder zijn gesynchroniseerd, maar vervolgens gefilterd, verwijderd in azure AD.

Voordat u begint met het maken van wijzigingen in filters, moet u ervoor zorgen dat u [de geplande taak uitschakelt](#disable-the-scheduled-task) , zodat u niet per ongeluk de wijzigingen exporteert die u nog niet hebt gecontroleerd.

Omdat met filteren veel objecten tegelijk kan worden verwijderd, moet u ervoor zorgen dat de nieuwe filters juist zijn voordat u wijzigingen in azure AD gaat exporteren. Nadat u de configuratie stappen hebt voltooid, raden we u ten zeerste aan de [verificaties tappen](#apply-and-verify-changes) te volgen voordat u Azure AD exporteert en wijzigt.

Om te voor komen dat u per ongeluk veel objecten verwijdert, is de functie '[onopzettelijke verwijderingen voor komen](how-to-connect-sync-feature-prevent-accidental-deletes.md)' standaard ingeschakeld. Als u veel objecten als gevolg van filtering (standaard 500) wilt verwijderen, moet u de stappen in dit artikel volgen om de verwijderingen door te laten gaan naar Azure AD.

Als u een build vóór november 2015 ([1.0.9125](reference-connect-version-history.md)) gebruikt, een wijziging aanbrengt in een filter configuratie en wachtwoord-hash-synchronisatie gebruikt, moet u een volledige synchronisatie van alle wacht woorden activeren nadat u de configuratie hebt voltooid. Zie [een volledige synchronisatie van alle wacht woorden activeren](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords)voor de stappen voor het activeren van een volledige synchronisatie met een wacht woord. Als u werkt met build 1.0.9125 of hoger, wordt in de normale **volledige synchronisatie** ook berekend of wacht woorden moeten worden gesynchroniseerd en als deze extra stap niet meer nodig is.

Als **gebruikers** objecten per ongeluk in azure AD zijn verwijderd vanwege een filter fout, kunt u de gebruikers objecten in azure AD opnieuw maken door uw filter configuraties te verwijderen. Vervolgens kunt u uw directory's opnieuw synchroniseren. Met deze actie worden de gebruikers teruggezet vanuit de Prullenbak in azure AD. U kunt echter geen andere object typen verwijderen. Als u bijvoorbeeld per ongeluk een beveiligings groep verwijdert en deze hebt gebruikt om een resource te ACL, kunnen de groep en de bijbehorende Acl's niet worden hersteld.

Met Azure AD Connect worden alleen objecten verwijderd die in het bereik zijn opgenomen. Als er objecten in azure AD zijn die zijn gemaakt door een andere synchronisatie-engine en deze objecten zich niet in het bereik bevinden, worden ze niet verwijderd door filters toe te voegen. Bijvoorbeeld, als u begint met een DirSync-server die een volledige kopie van uw hele directory in azure AD heeft gemaakt en u een nieuwe Azure AD Connect-synchronisatie Server Parallel installeert met filtering ingeschakeld, Azure AD Connect worden de extra objecten die zijn gemaakt door DirSync niet verwijderd.

De filter configuratie blijft behouden wanneer u een nieuwere versie van Azure AD Connect installeert of bijwerkt. Het is altijd een best practice om te controleren of de configuratie niet per ongeluk is gewijzigd na een upgrade naar een nieuwere versie voordat u de eerste synchronisatie cyclus uitvoert.

Als u meer dan één forest hebt, moet u de filter configuraties die in dit onderwerp worden beschreven, Toep assen op elk forest (ervan uitgaande dat u dezelfde configuratie voor al hen wilt).

### <a name="disable-the-scheduled-task"></a>De geplande taak uitschakelen
Voer de volgende stappen uit om de ingebouwde scheduler die elke 30 minuten een synchronisatie cyclus activeert, uit te scha kelen:

1. Ga naar een Power shell-prompt.
2. Voer uit `Set-ADSyncScheduler -SyncCycleEnabled $False` om de scheduler uit te scha kelen.
3. Breng de wijzigingen aan die in dit artikel worden beschreven.
4. Voer uit `Set-ADSyncScheduler -SyncCycleEnabled $True` om de Scheduler opnieuw in te scha kelen.

**Als u een Azure AD Connect-build gebruikt vóór 1.1.105.0**  
Voer de volgende stappen uit om de geplande taak uit te scha kelen die elke drie uur een synchronisatie cyclus activeert:

1. **Taak planner** starten vanuit het menu **Start** .
2. Zoek direct onder de **taak planner-bibliotheek** de taak met de naam **Azure AD Sync scheduler**, klik met de rechter muisknop en selecteer **uitschakelen**.  
   ![Taak planner](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. U kunt nu configuratie wijzigingen aanbrengen en de synchronisatie-engine hand matig uitvoeren vanuit de **Synchronization Service Manager** -console.

Wanneer u alle filter wijzigingen hebt voltooid, vergeet dan niet om terug te gaan en de taak opnieuw **in te scha kelen** .

## <a name="filtering-options"></a>Filter opties
U kunt de volgende filter configuratie typen Toep assen op het hulp programma Directory-synchronisatie:

* Op [**groep gebaseerd**](#group-based-filtering): filteren op basis van één groep kan alleen worden geconfigureerd tijdens de eerste installatie met behulp van de installatie wizard.
* [**Op domein gebaseerd**](#domain-based-filtering): met deze optie kunt u selecteren welke domeinen moeten worden gesynchroniseerd met Azure AD. U kunt ook domeinen toevoegen aan en verwijderen uit de configuratie van de synchronisatie-engine wanneer u wijzigingen aanbrengt in uw on-premises infra structuur nadat u Azure AD Connect synchronisatie hebt geïnstalleerd.
* [**Organisatie-eenheid (OE) – gebaseerd**](#organizational-unitbased-filtering): met deze optie kunt u selecteren welke organisatie-eenheden worden gesynchroniseerd met Azure AD. Deze optie is voor alle object typen in geselecteerde organisatie-eenheden.
* [**Op basis van kenmerken**](#attribute-based-filtering): met deze optie kunt u objecten filteren op basis van kenmerk waarden voor de objecten. U kunt ook verschillende filters hebben voor verschillende object typen.

U kunt op hetzelfde moment meerdere filter opties gebruiken. U kunt bijvoorbeeld filteren op basis van een organisatie-eenheid gebruiken om alleen objecten in één OE op te laten voegen. Op hetzelfde moment kunt u op kenmerken gebaseerde filtering gebruiken om de objecten verder te filteren. Wanneer u meerdere filter methoden gebruikt, gebruiken de filters een logische ' en ' tussen de filters.

## <a name="domain-based-filtering"></a>Filteren op basis van een domein
In deze sectie vindt u de stappen voor het configureren van uw domein filter. Als u domeinen in uw forest hebt toegevoegd of verwijderd nadat u Azure AD Connect hebt geïnstalleerd, moet u ook de filter configuratie bijwerken.

De aanbevolen manier om op domein gebaseerde filtering te wijzigen, is door de installatie wizard uit te voeren en het [filteren van domeinen en OE te](how-to-connect-install-custom.md#domain-and-ou-filtering)wijzigen. De installatie wizard automatiseert alle taken die in dit onderwerp worden beschreven.

Volg deze stappen alleen als u de installatie wizard om een of andere reden niet kunt uitvoeren.

Op domein gebaseerde filter configuratie bestaat uit de volgende stappen:

1. Selecteer de domeinen die u wilt toevoegen aan de synchronisatie.
2. Voor elk toegevoegd en verwijderd domein past u de uitvoerings profielen aan.
3. [Wijzigingen Toep assen en controleren](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>De domeinen selecteren die moeten worden gesynchroniseerd
Er zijn twee manieren om de domeinen te selecteren die moeten worden gesynchroniseerd:
    - De synchronisatie service gebruiken
    - Met de wizard Azure AD Connect.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>De domeinen selecteren die moeten worden gesynchroniseerd met de synchronisatie service
Voer de volgende stappen uit om het domein filter in te stellen:

1. Meld u aan bij de server waarop Azure AD Connect synchronisatie wordt uitgevoerd met behulp van een account dat lid is van de beveiligings groep **ADSyncAdmins** .
2. Start de **synchronisatie service** vanuit het menu **Start** .
3. Selecteer **connectors** en selecteer in de lijst **connectors** de Connector met het type **Active Directory Domain Services**. In **acties**, selecteert u **Eigenschappen**.  
   ![Connector eigenschappen](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klik op **mappartities configureren**.
5. Selecteer in de lijst **Directory partities selecteren** de optie en selecteer de selectie van de domeinen als dat nodig is. Controleer of alleen de partities die u wilt synchroniseren zijn geselecteerd.  
   ![Scherm opname van de Directory partities in het venster Eigenschappen.](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Als u uw on-premises Active Directory-infra structuur hebt gewijzigd en domeinen uit het forest hebt toegevoegd of verwijderd, klikt u op de knop **vernieuwen** om een bijgewerkte lijst op te halen. Wanneer u vernieuwt, wordt u gevraagd om referenties. Geef alle referenties met lees toegang tot Windows Server Active Directory op. Het hoeft niet de gebruiker te zijn die vooraf ingevuld is in het dialoog venster.  
   ![Vernieuwen is vereist](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Wanneer u klaar bent, sluit u het dialoog venster **Eigenschappen** door op **OK** te klikken. Als u domeinen uit het forest hebt verwijderd, ziet u een bericht pop-up met de melding dat een domein is verwijderd en dat de configuratie wordt opgeruimd.
7. Ga verder met het aanpassen van de uitvoerings profielen.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>De domeinen selecteren die moeten worden gesynchroniseerd met de wizard Azure AD Connect
Voer de volgende stappen uit om het domein filter in te stellen:

1.  De wizard Azure AD Connect starten
2.  Klik op **Configureren**
3.  Selecteer **synchronisatie opties aanpassen** en klik op **volgende**.
4.  Voer uw Azure AD-referenties in
5.  Klik in het scherm **verbonden directory's** op **volgende**.
6.  Klik op de **pagina domein en OE filteren** op **vernieuwen**.  Nieuwe domeinen worden nu weer gegeven, en verwijderde domeinen verdwijnen.
   ![Partities](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>De uitvoerings profielen bijwerken
Als u uw domein filter hebt bijgewerkt, moet u ook de uitvoerings profielen bijwerken.

1. Controleer in de lijst **Connect oren** of de connector die u in de vorige stap hebt gewijzigd, is geselecteerd. Selecteer in **acties** **Run-profielen configureren**.  
   ![Profielen voor connector uitvoering 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. De volgende profielen zoeken en identificeren:
    * Volledig importbewerking
    * Volledige synchronisatie
    * Delta-Import
    * Deltasynchronisatie
    * Exporteren
3. Voor elk profiel past u de **toegevoegde** en **Verwijderde** domeinen aan.
    1. Voor elk van de vijf profielen voert u de volgende stappen uit voor elk domein **toegevoegd** :
        1. Selecteer het uitvoerings profiel en klik op **nieuwe stap**.
        2. Selecteer op de pagina **stap configureren** in de vervolg keuzelijst **type** het stap type met de naam van het profiel dat u wilt configureren. Klik op **Volgende**.  
        ![Profielen voor connector uitvoering 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Selecteer op de pagina **connector configuratie** in de vervolg keuzelijst **partitie** de naam van het domein dat u aan uw domein filter hebt toegevoegd.  
        ![Profielen voor connector-uitvoering 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Klik op **volt ooien** om het dialoog venster **uitvoerings profiel configureren** te sluiten.
    2. Voor elk van de vijf profielen voert u de volgende stappen uit voor elk **verwijderd** domein:
        1. Selecteer het uitvoerings profiel.
        2. Als de **waarde** van het **partitie** kenmerk een GUID is, selecteert u de stap uitvoeren en klikt u op **stap verwijderen**.  
        ![Connector profielen uitvoeren 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Controleer de wijziging. Elk domein dat u wilt synchroniseren, moet worden vermeld als een stap in elk uitvoerings profiel.
4. Klik op **OK** om het dialoog venster **uitvoerings profielen configureren** te sluiten.
5.  Als u de configuratie wilt volt ooien, moet u een **volledige import** en een **Delta synchronisatie** uitvoeren. Ga verder met het lezen van de sectie [Apply en controleer de wijzigingen](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filteren op basis van een organisatie-eenheid
De voorkeurs manier om filteren op basis van een organisatie-eenheid te wijzigen, is door de installatie wizard uit te voeren en het [filteren van domeinen en OE te](how-to-connect-install-custom.md#domain-and-ou-filtering)wijzigen. De installatie wizard automatiseert alle taken die in dit onderwerp worden beschreven.

Volg deze stappen alleen als u de installatie wizard om een of andere reden niet kunt uitvoeren.

Voer de volgende stappen uit om filtering op basis van een organisatie-eenheid te configureren:

1. Meld u aan bij de server waarop Azure AD Connect synchronisatie wordt uitgevoerd met behulp van een account dat lid is van de beveiligings groep **ADSyncAdmins** .
2. Start de **synchronisatie service** vanuit het menu **Start** .
3. Selecteer **connectors** en selecteer in de lijst **connectors** de Connector met het type **Active Directory Domain Services**. In **acties**, selecteert u **Eigenschappen**.  
   ![Connector eigenschappen](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klik op **mappartities configureren**, selecteer het domein dat u wilt configureren en klik vervolgens op **containers**.
5. Wanneer u hierom wordt gevraagd, geeft u de referenties met lees toegang tot uw on-premises Active Directory op. Het hoeft niet de gebruiker te zijn die vooraf ingevuld is in het dialoog venster.
6. Wis in het dialoog venster **containers selecteren** de organisatie-eenheden die u niet wilt synchroniseren met de Cloud Directory en klik vervolgens op **OK**.  
   ![Organisatie-eenheden in het dialoog venster containers selecteren](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * De container **computers** moet worden geselecteerd om uw Windows 10-computers te synchroniseren met Azure AD. Als de computers die lid zijn van het domein zich in andere organisatie-eenheden bevinden, moet u ervoor zorgen dat deze zijn geselecteerd.
   * De container **ForeignSecurityPrincipals** moet zijn geselecteerd als er meerdere forests met vertrouwensrelaties zijn. Dankzij deze container kan het lidmaatschap van de beveiligingsgroep van verschillende forests worden omgezet.
   * Als u de functie voor het terugschrijven van apparaten hebt ingeschakeld, moet u de **RegisteredDevices** -OE selecteren. Als u een andere terugschrijf functie gebruikt, zoals de groep terugschrijven, moet u ervoor zorgen dat deze locaties zijn geselecteerd.
   * Selecteer een andere organisatie-eenheid waar gebruikers, iNetOrgPersons, groepen, contact personen en computers zich bevinden. In de afbeelding bevinden al deze organisatie-eenheden zich in de ManagedObjects-OE.
   * Als u filtering op basis van groepen gebruikt, moet de organisatie-eenheid waar de groep zich bevindt, worden opgenomen.
   * Houd er rekening mee dat u kunt configureren of nieuwe organisatie-eenheden die worden toegevoegd nadat de filter configuratie is voltooid, worden gesynchroniseerd of niet zijn gesynchroniseerd. Zie de volgende sectie voor details.
7. Wanneer u klaar bent, sluit u het dialoog venster **Eigenschappen** door op **OK** te klikken.
8. Als u de configuratie wilt volt ooien, moet u een **volledige import** en een **Delta synchronisatie** uitvoeren. Ga verder met het lezen van de sectie [Apply en controleer de wijzigingen](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Nieuwe organisatie-eenheden synchroniseren
Nieuwe Ou's die zijn gemaakt nadat het filter is geconfigureerd, worden standaard gesynchroniseerd. Deze status wordt aangegeven door een ingeschakeld selectie vakje. U kunt de selectie van sommige onderliggende organisatie-eenheden ook opheffen. Als u dit gedrag wilt weer geven, klikt u op het vak totdat het wit wordt met een blauw vinkje (de standaard status). Vervolgens schakelt u de selectie van de onderliggende organisatie-eenheden die u niet wilt synchroniseren.

Als alle onderliggende organisatie-eenheden zijn gesynchroniseerd, is het vak wit met een blauw vinkje.  
![OE met alle selectie vakjes](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Als sommige suborganisatie-eenheden niet zijn geselecteerd, is het vak grijs met een wit vinkje.  
![OE met een of meer suborganisatie-eenheden niet geselecteerd](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Met deze configuratie wordt een nieuwe organisatie-eenheid die is gemaakt onder ManagedObjects gesynchroniseerd.

Deze configuratie wordt altijd door de installatie wizard van Azure AD Connect gemaakt.

### <a name="dont-synchronize-new-ous"></a>Nieuwe organisatie-eenheden niet synchroniseren
U kunt de synchronisatie-engine zo configureren dat nieuwe organisatie-eenheden niet worden gesynchroniseerd nadat de filter configuratie is voltooid. Deze status wordt aangegeven in de gebruikers interface, omdat het vak effen grijs wordt weer gegeven zonder vinkje. Als u dit gedrag wilt weer geven, klikt u op het vak totdat het wit wordt zonder vinkje. Selecteer vervolgens de onderliggende organisatie-eenheden die u wilt synchroniseren.

![OE met de hoofdmap niet geselecteerd](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Met deze configuratie is een nieuwe organisatie-eenheid die is gemaakt onder ManagedObjects niet gesynchroniseerd.

## <a name="attribute-based-filtering"></a>Filteren op basis van kenmerken
Zorg ervoor dat u de[1.0.9125](reference-connect-version-history.md)(november 2015) of hoger gebruikt voor het werken met deze stappen.

> [!IMPORTANT]
>U wordt aangeraden de standaard regels die door **Azure AD Connect** zijn gemaakt, niet te wijzigen. Als u de regel wilt wijzigen, moet u deze klonen en de oorspronkelijke regel uitschakelen. Breng de gewenste wijzigingen aan in de gekloonde regel. Als u dit doet (door de oorspronkelijke regel uit te scha kelen), zult u eventuele oplossingen voor fouten of functies die via die regel zijn ingeschakeld, missen.

Op kenmerken gebaseerde filtering is de meest flexibele manier om objecten te filteren. U kunt de kracht van [declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md) gebruiken om bijna elk aspect te beheren wanneer een object wordt gesynchroniseerd met Azure AD.

U kunt [Inkomend](#inbound-filtering) filteren van Active Directory Toep assen op de tekst en [uitgaande](#outbound-filtering) filters van het omgekeerde naar Azure AD. U wordt aangeraden inkomende filtering toe te passen, omdat dat de eenvoudigste is om te onderhouden. Gebruik alleen uitgaande filtering als het nodig is om objecten van meer dan één forest samen te voegen voordat de evaluatie kan worden uitgevoerd.

### <a name="inbound-filtering"></a>Inkomende filtering
Bij inkomend filteren wordt de standaard configuratie gebruikt, waarbij objecten naar Azure AD moeten beschikken over het cloudFiltered-kenmerk niet zijn ingesteld op een waarde die moet worden gesynchroniseerd. Als de waarde van dit kenmerk is ingesteld op **True**, is het object niet gesynchroniseerd. Het mag niet worden ingesteld op **Onwaar**. Om ervoor te zorgen dat andere regels een waarde kunnen bijdragen, mag dit kenmerk alleen de waarden **True** of **Null** (afwezig) hebben.

Bij inkomend filteren gebruikt u de kracht van **bereik** om te bepalen welke objecten moeten worden gesynchroniseerd of niet moeten worden gesynchroniseerd. Hier maakt u aanpassingen aan de vereisten van uw eigen organisatie. De scope module heeft een **groep** en een **component** om te bepalen wanneer een synchronisatie regel binnen het bereik valt. Een groep bevat een of meer componenten. Er bevindt zich een logische ' AND ' tussen meerdere componenten en een logische ' OR ' of ' tussen meerdere groepen.

Laten we een voor beeld bekijken:  
![Een scherm opname met een voor beeld van het toevoegen van bereik filters.](./media/how-to-connect-sync-configure-filtering/scope.png)  
Dit moet worden gelezen als **(afdeling = IT) of (afdeling = verkoop en c = US)**.

In de volgende voor beelden en stappen gebruikt u het gebruikers object als voor beeld, maar u kunt dit voor alle object typen gebruiken.

In de volgende voor beelden begint de prioriteits waarde met 50. Dit kan een wille keurig getal zijn dat niet wordt gebruikt, maar moet lager zijn dan 100.

#### <a name="negative-filtering-do-not-sync-these"></a>Negatief filteren: "deze niet synchroniseren"
In het volgende voor beeld filtert u (niet synchroniseren) alle gebruikers waarbij **extensionAttribute15** de waarde **NoSync** heeft.

1. Meld u aan bij de server waarop Azure AD Connect synchronisatie wordt uitgevoerd met behulp van een account dat lid is van de beveiligings groep **ADSyncAdmins** .
2. Start de **Editor voor synchronisatie regels** vanuit het menu **Start** .
3. Zorg ervoor dat **Inkomend** is geselecteerd en klik op **nieuwe regel toevoegen**.
4. Geef een beschrijvende naam op voor de regel, zoals '*in AD-User DoNotSyncFilter*'. Selecteer het juiste forest, selecteer **gebruiker** als het **object type CS** en selecteer **persoon** als het **object type MV**. Selecteer in **type koppeling** de optie **samen voegen**. Typ in het veld voor **rang** een waarde die momenteel niet wordt gebruikt door een andere synchronisatie regel (bijvoorbeeld 50) en klik vervolgens op **volgende**.  
   ![Beschrijving inkomende 1](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Klik in het **filter bereik** op **groep toevoegen** en klik op **component toevoegen**. Selecteer **Attribute** in het kenmerk **ExtensionAttribute15**. Zorg ervoor dat de **operator** is ingesteld op **gelijk** en typ de waarde **NoSync** in het vak **waarde** . Klik op **Volgende**.  
   ![Inkomend 2 bereik](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Laat de regels voor **samen voegen** leeg en klik op **volgende**.
7. Klik op **trans formatie toevoegen**, selecteer de **FlowType** als **constante** en selecteer **cloudFiltered** als **doel kenmerk**. Typ in het tekstvak **bron** de **waarde waar**. Klik op **toevoegen** om de regel op te slaan.  
   ![Inkomende 3-trans formatie](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Als u de configuratie wilt volt ooien, moet u een **volledige synchronisatie** uitvoeren. Ga verder met het lezen van de sectie [Apply en controleer de wijzigingen](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positieve filtering: alleen deze synchroniseren
Het uitdrukken van positieve filtering kan lastiger zijn omdat u ook objecten moet overwegen die niet duidelijk zijn om te worden gesynchroniseerd, zoals Vergader zalen. U gaat ook het standaard filter in de out-of-Box-regel negeren **in van de AD-User-koppeling**. Wanneer u een aangepast filter maakt, moet u ervoor zorgen dat u geen essentiële systeem objecten, replicatie conflict objecten, speciale post vakken en de service accounts voor Azure AD Connect opneemt.

Voor de optie voor positieve filtering zijn twee synchronisatie regels vereist. U hebt één regel (of meerdere) nodig met het juiste bereik van objecten om te synchroniseren. U hebt ook een tweede catch-all Sync-regel nodig waarmee alle objecten worden gefilterd die nog niet zijn geïdentificeerd als een object dat moet worden gesynchroniseerd.

In het volgende voor beeld synchroniseert u alleen gebruikers objecten waarbij het kenmerk afdeling de waarde **Sales** heeft.

1. Meld u aan bij de server waarop Azure AD Connect synchronisatie wordt uitgevoerd met behulp van een account dat lid is van de beveiligings groep **ADSyncAdmins** .
2. Start de **Editor voor synchronisatie regels** vanuit het menu **Start** .
3. Zorg ervoor dat **Inkomend** is geselecteerd en klik op **nieuwe regel toevoegen**.
4. Geef een beschrijvende naam op voor de regel, zoals '*in van AD-gebruikers verkoop synchroniseren*'. Selecteer het juiste forest, selecteer **gebruiker** als het **object type CS** en selecteer **persoon** als het **object type MV**. Selecteer in **type koppeling** de optie **samen voegen**. Typ in het veld voor **rang** een waarde die momenteel niet wordt gebruikt door een andere synchronisatie regel (bijvoorbeeld 51) en klik vervolgens op **volgende**.  
   ![Beschrijving van binnenkomend 4](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Klik in het **filter bereik** op **groep toevoegen** en klik op **component toevoegen**. Selecteer in **kenmerk** **afdeling**. Zorg ervoor dat operator is ingesteld op **gelijk** en typ de waarde **Sales** in het vak **waarde** . Klik op **Volgende**.  
   ![Inkomend 5 bereik](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Laat de regels voor **samen voegen** leeg en klik op **volgende**.
7. Klik op **trans formatie toevoegen**, selecteer **constante** als de **FlowType** en selecteer de **cloudFiltered** als **doel kenmerk**. Typ **Onwaar** in het vak **bron** . Klik op **toevoegen** om de regel op te slaan.  
   ![Binnenkomende 6-trans formatie](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Dit is een speciaal geval waarin u cloudFiltered expliciet instelt op **False**.
8. We moeten nu de regel voor het synchroniseren van de catch-out maken. Geef een beschrijvende naam op voor de regel, zoals '*in van AD: gebruiker catch all filter*'. Selecteer het juiste forest, selecteer **gebruiker** als het **object type CS** en selecteer **persoon** als het **object type MV**. Selecteer in **type koppeling** de optie **samen voegen**. Typ in het veld voor **rang** een waarde die momenteel niet wordt gebruikt door een andere synchronisatie regel (bijvoorbeeld 99). U hebt een prioriteits waarde geselecteerd die hoger is (lagere prioriteit) dan de vorige synchronisatie regel. Maar u hebt ook enige ruimte gelaten, zodat u later meer filter synchronisatie regels kunt toevoegen wanneer u wilt beginnen met het synchroniseren van aanvullende afdelingen. Klik op **Volgende**.  
   ![Beschrijving binnenkomende 7](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Laat het **bereik filter** leeg en klik op **volgende**. Een leeg filter geeft aan dat de regel op alle objecten moet worden toegepast.
10. Laat de regels voor **samen voegen** leeg en klik op **volgende**.
11. Klik op **trans formatie toevoegen**, selecteer **constante** als de **FlowType** en selecteer **cloudFiltered** als **doel kenmerk**. Typ in het vak **bron** de **waarde waar**. Klik op **toevoegen** om de regel op te slaan.  
    ![Inkomende 3-trans formatie](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Als u de configuratie wilt volt ooien, moet u een **volledige synchronisatie** uitvoeren. Ga verder met het lezen van de sectie [Apply en controleer de wijzigingen](#apply-and-verify-changes).

Als dat het geval is, kunt u meer regels van het eerste type maken waarbij u meer objecten in de synchronisatie opneemt.

### <a name="outbound-filtering"></a>Uitgaande filters
In sommige gevallen is het alleen nodig om de filtering uit te voeren nadat de objecten zijn toegevoegd aan de tekst. Het kan bijvoorbeeld nodig zijn om te kijken naar het kenmerk mail van het bron-forest en het kenmerk userPrincipalName van het account-forest om te bepalen of een object moet worden gesynchroniseerd. In deze gevallen maakt u het filter op de uitgaande regel.

In dit voor beeld wijzigt u de filtering zodat alleen gebruikers met hun e-mail en userPrincipalName eindigend @contoso.com worden gesynchroniseerd:

1. Meld u aan bij de server waarop Azure AD Connect synchronisatie wordt uitgevoerd met behulp van een account dat lid is van de beveiligings groep **ADSyncAdmins** .
2. Start de **Editor voor synchronisatie regels** vanuit het menu **Start** .
3. Klik onder **regel type** op **uitgaand**.
4. Afhankelijk van de versie van de verbinding die u gebruikt, vindt u de regel met de naam **naar Azure AD: de gebruiker wordt toegevoegd** aan of verwijderd **uit Azure AD-gebruiker toevoegen SOAInAD** en klikt u op **bewerken**.
5. Beantwoord in het pop-upvenster **Ja** om een kopie van de regel te maken.
6. Op de pagina **Beschrijving** wijzigt u de **prioriteit** in een ongebruikte waarde, zoals 50.
7. Klik op het **filter bereik** op de navigatie balk aan de linkerkant en klik vervolgens op **component toevoegen**. Selecteer **Attribute** in het kenmerk **mail**. Selecteer in **operator** **ENDSWITH**. Typ **\@ contoso.com** in het **veld waarde** en klik vervolgens op **component toevoegen**. Selecteer **Attribute** in het kenmerk **userPrincipalName**. Selecteer in **operator** **ENDSWITH**. Typ **\@ contoso.com** in het **veld waarde**.
8. Klik op **Opslaan**.
9. Als u de configuratie wilt volt ooien, moet u een **volledige synchronisatie** uitvoeren. Ga verder met het lezen van de sectie [Apply en controleer de wijzigingen](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Wijzigingen Toep assen en controleren
Nadat u de configuratie wijzigingen hebt aangebracht, moet u deze Toep assen op de objecten die al in het systeem aanwezig zijn. Het kan ook zijn dat de objecten die zich momenteel niet in de synchronisatie-engine bevinden, moeten worden verwerkt (en de synchronisatie-engine moet het bron systeem opnieuw lezen om de inhoud ervan te controleren).

Als u de configuratie hebt gewijzigd door gebruik te maken van **domein** **-of organisatie-eenheid** filtering, moet u een **volledige import bewerking** uitvoeren, gevolgd door **Delta synchronisatie**.

Als u de configuratie hebt gewijzigd met **kenmerk** filtering, moet u een **volledige synchronisatie** uitvoeren.

Voer de volgende stappen uit:

1. Start de **synchronisatie service** vanuit het menu **Start** .
2. Selecteer **connectors**. Selecteer in de lijst **connectors** de connector waar u eerder een configuratie wijziging hebt aangebracht. In **acties**, selecteer **uitvoeren**.  
   ![Uitvoering van connector](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Selecteer in **profielen uitvoeren** de bewerking die is vermeld in de vorige sectie. Als u twee acties wilt uitvoeren, voert u de tweede actie uit nadat de eerste is voltooid. (De **status** kolom is **niet actief** voor de geselecteerde connector.)

Na de synchronisatie worden alle wijzigingen klaargezet om te worden geëxporteerd. Voordat u de wijzigingen in azure AD daad werkelijk aanbrengt, moet u controleren of al deze wijzigingen juist zijn.

1. Start een opdracht prompt en ga naar `%ProgramFiles%\Microsoft Azure AD Sync\bin` .
2. Voer `csexport "Name of Connector" %temp%\export.xml /f:x` uit.  
   De naam van de connector bevindt zich in de synchronisatie service. Het heeft een naam die vergelijkbaar is met ' contoso.com-azure AD ' voor Azure AD.
3. Voer `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` uit.
4. U hebt nu een bestand in% Temp% met de naam export.csv dat in micro soft Excel kan worden onderzocht. Dit bestand bevat alle wijzigingen die moeten worden geëxporteerd.
5. Breng de benodigde wijzigingen aan in de gegevens of configuratie en voer deze stappen opnieuw uit (importeren, synchroniseren en verifiëren) totdat de wijzigingen die worden geëxporteerd, zijn wat u verwacht.

Als u tevreden bent, exporteert u de wijzigingen naar Azure AD.

1. Selecteer **connectors**. Selecteer in de lijst **Connect oren** de Azure AD-connector. In **acties**, selecteer **uitvoeren**.
2. Selecteer in **Run Profiles** de optie **export**.
3. Als uw configuratie wijzigingen veel objecten verwijderen, ziet u een fout in de export wanneer het aantal groter is dan de geconfigureerde drempel waarde (standaard 500). Als u deze fout ziet, moet u de functie '[onopzettelijke verwijderingen voor komen](how-to-connect-sync-feature-prevent-accidental-deletes.md)' tijdelijk uitschakelen.

Nu is het tijd om de Scheduler opnieuw in te scha kelen.

1. **Taak planner** starten vanuit het menu **Start** .
2. Zoek direct onder de **taak planner-bibliotheek** de taak met de naam **Azure AD Sync scheduler**, klik met de rechter muisknop en selecteer **inschakelen**.

## <a name="group-based-filtering"></a>Filteren op basis van een groep
U kunt filteren op basis van een groep configureren de eerste keer dat u Azure AD Connect installeert met behulp van [aangepaste installatie](how-to-connect-install-custom.md#sync-filtering-based-on-groups). Het is bedoeld voor een pilot implementatie waarbij u slechts een klein aantal objecten wilt synchroniseren. Wanneer u filteren op basis van groepen uitschakelt, kan het niet meer worden ingeschakeld. Het wordt *niet ondersteund* voor het gebruik van filteren op basis van groepen in een aangepaste configuratie. Het wordt alleen ondersteund voor het configureren van deze functie met behulp van de installatie wizard. Wanneer u de pilot hebt voltooid, gebruikt u een van de andere filter opties in dit onderwerp. Wanneer u filtering op basis van een organisatie-eenheid gebruikt in combi natie met filteren op basis van een groep, moeten de OE (s) waar de groep en de leden ervan zich bevinden, worden opgenomen.

Bij het synchroniseren van meerdere AD-forests kunt u filteren op basis van een groep configureren door voor elke AD-connector een andere groep op te geven. Als u een gebruiker wilt synchroniseren in één AD-forest en dezelfde gebruiker een of meer overeenkomende objecten in andere AD-forests heeft, moet u ervoor zorgen dat het gebruikers object en alle bijbehorende objecten zich in groeps filter bereik bevinden. Voorbeelden:

* U hebt een gebruiker in een forest met een bijbehorend FSP-object (Foreign Security Principal) in een ander forest. Beide objecten moeten zich in een groeps filter bereik bevinden. Anders wordt de gebruiker niet gesynchroniseerd met Azure AD.

* U hebt een gebruiker in een forest met een bijbehorend resource account (bijvoorbeeld een gekoppeld postvak) in een ander forest. Verder hebt u Azure AD Connect geconfigureerd om de gebruiker te koppelen aan het resource-account. Beide objecten moeten zich in een groeps filter bereik bevinden. Anders wordt de gebruiker niet gesynchroniseerd met Azure AD.

* U hebt een gebruiker in het ene forest met een bijbehorend e-mail adres in een ander forest. Verder hebt u Azure AD Connect geconfigureerd om de gebruiker te koppelen aan de contact persoon van de e-mail. Beide objecten moeten zich in een groeps filter bereik bevinden. Anders wordt de gebruiker niet gesynchroniseerd met Azure AD.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.
- Meer informatie over [het integreren van uw on-premises identiteiten met Azure AD](whatis-hybrid-identity.md).
