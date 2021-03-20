---
title: Aangepaste standaard regels herstellen-Azure AD Connect | Microsoft Docs
description: Meer informatie over het oplossen van aangepaste standaard regels die bij Azure AD Connect worden geleverd.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91320044"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Aangepaste standaard regels in Azure AD Connect herstellen

Azure Active Directory (Azure AD) Connect maakt gebruik van standaard regels voor synchronisatie.  Helaas zijn deze regels niet universeel van toepassing op alle organisaties. Op basis van uw vereisten moet u deze mogelijk wijzigen. In dit artikel worden twee voor beelden van de meest voorkomende aanpassingen beschreven en wordt uitgelegd hoe u deze aanpassingen kunt verhelpen.

>[!NOTE] 
> Het wijzigen van bestaande standaard regels om een vereiste aanpassing te verzorgen, wordt niet ondersteund. Als u dit doet, wordt voor komen dat deze regels in toekomstige releases worden bijgewerkt naar de meest recente versie. U beschikt niet over de oplossingen die u nodig hebt, of nieuwe functies. In dit document wordt uitgelegd hoe u hetzelfde resultaat kunt krijgen zonder de bestaande standaard regels te wijzigen. 

## <a name="how-to-identify-modified-default-rules"></a>Aangepaste standaard regels identificeren
Vanaf versie 1.3.7.0 van Azure AD Connect is het eenvoudig om de gewijzigde standaard regel te identificeren. Ga naar **apps op het bureau blad** en selecteer **Editor voor synchronisatie regels**.

![Azure AD Connect, met de editor voor synchronisatie regels gemarkeerd](media/how-to-connect-fix-default-rules/default1.png)

In de editor worden alle gewijzigde standaard regels weer gegeven met een waarschuwings pictogram vóór de naam.

![Waarschuwingspictogram](media/how-to-connect-fix-default-rules/default2.png)

 Er wordt ook een uitgeschakelde regel met dezelfde naam weer gegeven (dit is de standaard regel voor standaard).

![Editor voor synchronisatie regels, met standaard standaard regel en aangepaste standaard regel](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Algemene aanpassingen
Hier volgen enkele algemene aanpassingen aan de standaard regels:

- Kenmerk stroom wijzigen
- Bereik filter wijzigen
- Koppelings voorwaarde wijzigen

Voordat u regels wijzigt:

- Schakel de synchronisatie planner uit. De scheduler wordt standaard elke 30 minuten uitgevoerd. Zorg ervoor dat deze niet begint terwijl u wijzigingen aanbrengt en uw nieuwe regels bijwerkt. Als u de scheduler tijdelijk wilt uitschakelen, start u Power shell en voert u uit `Set-ADSyncScheduler -SyncCycleEnabled $false` .
 ![Power shell-opdrachten om de synchronisatie planner uit te scha kelen](media/how-to-connect-fix-default-rules/default3.png)

- De wijziging in het bereik filter kan ertoe leiden dat objecten in de doel directory worden verwijderd. Wees voorzichtig voordat u wijzigingen aanbrengt in de scope-objecten. U wordt aangeraden wijzigingen aan te brengen in een staging-server voordat u wijzigingen aanbrengt op de actieve server.
- Voer een preview uit op één object, zoals vermeld in de sectie [synchronisatie regel valideren](#validate-sync-rule) nadat een nieuwe regel is toegevoegd.
- Voer een volledige synchronisatie uit na het toevoegen van een nieuwe regel of het wijzigen van een aangepaste synchronisatie regel. Deze synchronisatie past nieuwe regels toe op alle objecten.

## <a name="change-attribute-flow"></a>Kenmerk stroom wijzigen
Er zijn drie verschillende scenario's voor het wijzigen van de kenmerk stroom:
- Een nieuw kenmerk toevoegen.
- De waarde van een bestaand kenmerk overschrijven.
- Er wordt gekozen om een bestaand kenmerk niet te synchroniseren.

U kunt dit doen zonder de standaard regels te wijzigen.

### <a name="add-a-new-attribute"></a>Een nieuw kenmerk toevoegen
Als u merkt dat een kenmerk niet vanuit uw bronmap naar de doel directory stroomt, gebruikt u de [Azure AD Connect Sync: Directory-extensies](how-to-connect-sync-feature-directory-extensions.md) om dit probleem op te lossen.

Als de uitbrei dingen niet werken, kunt u proberen om twee nieuwe synchronisatie regels toe te voegen, zoals beschreven in de volgende secties.


#### <a name="add-an-inbound-sync-rule"></a>Een regel voor binnenkomende synchronisatie toevoegen
Een binnenkomende Sync-regel betekent dat de bron voor het kenmerk een connector ruimte is en dat het doel de tekst is. Als u bijvoorbeeld een nieuwe kenmerk stroom van on-premises Active Directory naar Azure Active Directory wilt maken, maakt u een nieuwe regel voor binnenkomende synchronisatie. Start de **Editor voor synchronisatie regels**, selecteer **binnenkomend** als richting en selecteer **nieuwe regel toevoegen**. 

 ![Scherm opname waarin de ' Synchronization Rules editor ' wordt weer gegeven en ' Inkomend ' en ' nieuwe regel toevoegen ' is geselecteerd.](media/how-to-connect-fix-default-rules/default3a.png)

Volg uw eigen naam Conventie om de regel een naam te geven. Hier gebruiken we **aangepaste in van de AD-gebruiker**. Dit betekent dat de regel een aangepaste regel is en een regel voor binnenkomend verkeer is van de Active Directory-Connector ruimte naar de omgekeerde tekst.   

 ![Regel voor binnenkomende synchronisatie maken](media/how-to-connect-fix-default-rules/default3b.png)

Geef uw eigen beschrijving van de regel op, zodat het toekomstig onderhoud van de regel eenvoudig is. De beschrijving kan bijvoorbeeld worden gebaseerd op het doel van de regel en waarom deze nodig is.

Maak uw selecties voor het **verbonden systeem**, het **object type verbonden systeem** en de velden voor het **object type** van de tekst.

Geef een prioriteits waarde op van 0 tot en met 99 (het lagere nummer, des te hoger de prioriteit). Gebruik de standaard selecties voor het **Label**, **Schakel wachtwoord synchronisatie** en **Uitgeschakelde** velden in.

Laat het **bereik filter** leeg. Dit betekent dat de regel van toepassing is op alle objecten die zijn gekoppeld tussen het Active Directory verbonden systeem en het omgekeerde.

Bewaar **regels voor samen voegen** leeg. Dit betekent dat deze regel de samenvoegings voorwaarde gebruikt die is gedefinieerd in de standaard regel standaard. Dit is een andere reden om de standaard regel standaard niet uit te scha kelen of te verwijderen. Als er geen deelname voorwaarde is, loopt het kenmerk niet over. 

Voeg de juiste trans formaties voor uw kenmerk toe. U kunt een constante toewijzen om een constante waarde stroom te maken naar uw doel kenmerk. U kunt directe toewijzing tussen het bron-of doel kenmerk gebruiken. U kunt ook een expressie gebruiken voor het kenmerk. Hier volgen diverse [expressie functies](./reference-connect-sync-functions-reference.md) die u kunt gebruiken.

#### <a name="add-an-outbound-sync-rule"></a>Een uitgaande synchronisatie regel toevoegen
Als u het kenmerk aan de doel directory wilt koppelen, moet u een uitgaande regel maken. Dit betekent dat de bron het omgekeerde is en dat het doel het verbonden systeem is. Als u een uitgaande regel wilt maken, start u de **Editor voor synchronisatie regels**, wijzigt u de **richting** in **uitgaand** en selecteert u **nieuwe regel toevoegen**. 

![Editor voor synchronisatie regels](media/how-to-connect-fix-default-rules/default3c.png)

Net als bij de regel voor binnenkomende verbindingen kunt u uw eigen naamgevings Conventie gebruiken om de regel een naam te geven. Selecteer het **verbonden systeem** als de Azure AD-Tenant en selecteer het verbonden systeem object waarvoor u de kenmerk waarde wilt instellen. Stel de prioriteit in van 0 tot en met 99. 

![Regel voor uitgaande synchronisatie maken](media/how-to-connect-fix-default-rules/default3d.png)

Bewaar het **bereik filter** en de **regels voor samen voegen** leeg. Vul de trans formatie in als constante, direct of expressie. 

U weet nu hoe u een nieuw kenmerk voor een gebruikers object stroom kunt maken van Active Directory naar Azure Active Directory. U kunt deze stappen gebruiken om elk kenmerk van elk object toe te wijzen aan de bron en het doel. Zie [Aangepaste synchronisatie regels maken](how-to-connect-create-custom-sync-rule.md) en voor [bereiding voor het inrichten van gebruikers](/office365/enterprise/prepare-for-directory-synchronization)voor meer informatie.

### <a name="override-the-value-of-an-existing-attribute"></a>De waarde van een bestaand kenmerk overschrijven
Mogelijk wilt u de waarde overschrijven van een kenmerk dat al is toegewezen. Als u bijvoorbeeld altijd een null-waarde wilt instellen op een kenmerk in azure AD, hoeft u alleen maar een regel voor binnenkomende verbindingen te maken. Maak de constante waarde, `AuthoritativeNull` , stroom naar het doel kenmerk. 

>[!NOTE] 
> Gebruik `AuthoritativeNull` in plaats van `Null` in dit geval. Dit komt omdat de niet-null-waarde de null-waarde vervangt, zelfs als deze een lagere prioriteit heeft (een hogere waarde in de regel). `AuthoritativeNull`daarentegen wordt niet vervangen door andere regels die niet null zijn. 

### <a name="dont-sync-existing-attribute"></a>Bestaand kenmerk niet synchroniseren
Als u een kenmerk wilt uitsluiten van synchronisatie, gebruikt u de functie voor kenmerk filtering in Azure AD Connect. Start **Azure AD Connect** vanaf het bureaublad pictogram en selecteer vervolgens **synchronisatie opties aanpassen**.

![Opties Azure AD Connect extra taken](media/how-to-connect-fix-default-rules/default4.png)

 Zorg ervoor dat **Azure AD-app-en-kenmerk filters** zijn geselecteerd en selecteer **volgende**.

![Optionele functies Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Wis de kenmerken die u wilt uitsluiten van synchronisatie.

![Azure AD Connect kenmerken](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Bereik filter wijzigen
Azure AD Sync zorgt voor de meeste van de objecten. U kunt het bereik van objecten verminderen en het aantal objecten beperken dat moet worden geëxporteerd, zonder de standaard regels voor synchronisatie te wijzigen. 

Gebruik een van de volgende methoden om het bereik van de objecten die u synchroniseert te beperken:

- cloudFiltered-kenmerk
- Organisatie-eenheid filteren

Als u het bereik van de gesynchroniseerde gebruikers vermindert, wordt de wachtwoord-hash-synchronisatie ook gestopt voor de gefilterde gebruikers. Als de objecten al worden gesynchroniseerd, worden de gefilterde objecten verwijderd uit de doel Directory nadat u het bereik hebt verminderd. Zorg er daarom voor dat u de scope zorgvuldig kunt bereiken.

>[!IMPORTANT] 
> Het verg Roten van het bereik van objecten die door Azure AD Connect zijn geconfigureerd, wordt niet aanbevolen. Dit maakt het voor het ondersteunings team van micro soft lastig om de aanpassingen te begrijpen. Als u het bereik van objecten moet verhogen, bewerkt u de bestaande regel, kloont u deze en schakelt u de oorspronkelijke regel uit. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered-kenmerk
U kunt dit kenmerk niet instellen in Active Directory. Stel de waarde van dit kenmerk in door een nieuwe regel voor binnenkomende verbindingen toe te voegen. U kunt vervolgens **trans formatie** en **expressie** gebruiken om dit kenmerk in de tekst in te stellen. In het volgende voor beeld ziet u dat u niet alle gebruikers wilt synchroniseren waarvan de afdelings naam begint met **hrd** (niet hoofdletter gevoelig):

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

We hebben de afdeling eerst van de bron (Active Directory) geconverteerd naar kleine letters. Vervolgens `Left` hebben we met de functie alleen de eerste drie tekens en worden deze vergeleken met `hrd` . Als deze overeenkomt, wordt de waarde ingesteld op `True` , anders `NULL` . Bij het instellen van de waarde op NULL, kan een andere regel met een lagere prioriteit (een hogere waarde) ernaar schrijven met een andere voor waarde. Voer Preview uit op één object om de synchronisatie regel te valideren, zoals vermeld in de sectie [synchronisatie regel valideren](#validate-sync-rule) .

![Opties voor de inkomende synchronisatie regel maken](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Organisatie-eenheid filteren
U kunt een of meer organisatie-eenheden (Ou's) maken en de objecten die u niet wilt synchroniseren naar deze Ou's verplaatsen. Configureer vervolgens de OE-filtering in Azure AD Connect. Start **Azure AD Connect** vanaf het bureaublad pictogram en selecteer de volgende opties. U kunt de OE-filtering ook configureren op het moment van de installatie van Azure AD Connect. 

![Azure AD Connect extra taken](media/how-to-connect-fix-default-rules/default8.png)

Volg de wizard en wis de organisatie-eenheden die u niet wilt synchroniseren.

![Opties voor filteren van domein en OE Azure AD Connect](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Koppelings voorwaarde wijzigen
Gebruik de standaard waarden voor samen voegen die worden geconfigureerd door Azure AD Connect. Als u standaard waarden voor samen voegen wijzigt, is het lastig voor micro soft ondersteuning om de aanpassingen te begrijpen en het product te ondersteunen.

## <a name="validate-sync-rule"></a>Synchronisatie regel valideren
U kunt de zojuist toegevoegde synchronisatie regel valideren met behulp van de preview-functie, zonder de volledige synchronisatie cyclus uit te voeren. Selecteer in Azure AD Connect de optie **synchronisatie service**.

![Azure AD Connect, waarbij de synchronisatie service is gemarkeerd](media/how-to-connect-fix-default-rules/default10.png)

Selecteer **zoektekst zoeken**. Selecteer het bereik object als **persoon**, selecteer **component toevoegen** en vermeld uw zoek criteria. Selecteer vervolgens **zoeken** en dubbel klik op het object in de zoek resultaten. Zorg ervoor dat uw gegevens in Azure AD Connect up-to-date zijn voor dat object door import en sync uit te voeren op het forest voordat u deze stap uitvoert.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Selecteer op **Eigenschappen van omgekeerd object** **connectors**, selecteer het object in de bijbehorende connector (Forest) en selecteer **Eigenschappen...**.

![Eigenschappen van het omgekeerde object](media/how-to-connect-fix-default-rules/default12.png)

**Voor beeld selecteren...**

![Eigenschappen van connector Space-object](media/how-to-connect-fix-default-rules/default13a.png)

Selecteer in het venster voor beeld de optie **Preview genereren** en **kenmerk stroom importeren** in het linkerdeel venster.

![Scherm opname van het venster ' preview ' met ' kenmerk stroom importeren ' en ' voor beeld genereren ' geselecteerd.](media/how-to-connect-fix-default-rules/default14.png)
 
Hier ziet u dat de zojuist toegevoegde regel wordt uitgevoerd op het object en dat het kenmerk is ingesteld `cloudFiltered` op True.

![Preview](media/how-to-connect-fix-default-rules/default15a.png)
 
Als u de gewijzigde regel met de standaard regel wilt vergelijken, exporteert u beide regels afzonderlijk, als tekst bestanden. Deze regels worden geëxporteerd als een Power shell-script bestand. U kunt deze vergelijken met behulp van een bestandsvergelijkings programma (bijvoorbeeld Windiff) om de wijzigingen weer te geven. 
 
U ziet dat in de gewijzigde regel het `msExchMailboxGuid` kenmerk wordt gewijzigd in het **expressie** type in plaats van **direct**. De waarde wordt ook gewijzigd in **Null** en de optie **ExecuteOnce** . U kunt geïdentificeerde en prioriteits verschillen negeren. 

![uitvoer van Windiff-hulpprogram ma's](media/how-to-connect-fix-default-rules/default17.png)
 
Als u uw regels wilt herstellen om ze terug te zetten naar de standaard instellingen, verwijdert u de gewijzigde regel en schakelt u de standaard regel in. Zorg ervoor dat u niet de aanpassing kwijtraakt die u probeert te verkrijgen. Wanneer u klaar bent, voert u **volledige synchronisatie** uit.

## <a name="next-steps"></a>Volgende stappen
- [Hardware en vereisten](how-to-connect-install-prerequisites.md) 
- [Snelle instellingen](how-to-connect-install-express.md)
- [Aangepaste instellingen](how-to-connect-install-custom.md)