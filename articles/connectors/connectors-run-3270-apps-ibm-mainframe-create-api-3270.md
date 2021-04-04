---
title: Verbinding maken met 3270-apps op IBM-mainframes
description: Met behulp van Azure Logic Apps en IBM 3270-connector integreert u 3270 op het scherm gestuurde apps met Azure.
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 02/03/2021
tags: connectors
ms.openlocfilehash: 7873076618125241d9bdf4f225c194572553d060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573429"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Op het scherm gestuurde apps op IBM-mainframes 3270 integreren met Azure met behulp van Azure Logic Apps en IBM 3270-connector

Met Azure Logic Apps en de IBM 3270-connector kunt u IBM mainframe-apps openen en uitvoeren waar u doorgaans komt via emulatorschermen van 3270. Op die manier kunt u uw IBM mainframe-apps integreren met Azure-, Microsoft- en andere apps, services en systemen door geautomatiseerde werkstromen te maken met Azure Logic Apps. De connector communiceert met IBM-mainframes met behulp van het TN3270-protocol en is beschikbaar in alle Azure Logic Apps-regio's, met uitzondering van Azure Government en Azure China 21Vianet. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

In dit artikel worden de volgende aspecten beschreven voor het gebruik van de 3270-connector: 

* Waarom de IBM 3270-connector in Azure Logic Apps gebruiken en hoe de connector op 3270 scherm gestuurde apps draait

* De vereisten en de installatie voor het gebruik van de 3270-connector

* De stappen voor het toevoegen van 3270-connector acties aan uw logische app

## <a name="why-use-this-connector"></a>Waarom deze connector gebruiken?

Om toegang te krijgen tot apps op IBM-mainframes, gebruikt u meestal een 3270-terminal emulator, ook wel een ' groen scherm ' genoemd. Deze methode is een tijdgeteste manier, maar heeft beperkingen. Hoewel HIS (Host Integration Server) u helpt om rechtstreeks met deze apps te werken, is het mogelijk dat het scherm niet kan worden gescheiden en dat de bedrijfs logica niet mogelijk is. Of misschien hebt u niet langer informatie over de werking van de hosttoepassingen.

Om deze scenario's uit te breiden, kunt u de IBM 3270-connector in Azure Logic Apps gebruiken met het ontwerp hulpprogramma van 3270, dat u gebruikt om te registreren, of ' vastleggen ', de host-schermen die worden gebruikt voor een specifieke taak, de navigatie stroom voor die taak definiëren via uw mainframe-app en de methoden definiëren met de para meters voor invoer en uitvoer voor die taak. Het hulp programma voor ontwerpen converteert die informatie naar meta gegevens die de 3270-connector gebruikt bij het aanroepen van een actie die de taak van uw logische app vertegenwoordigt.

Nadat u het bestand met meta gegevens hebt gegenereerd vanuit het ontwerp hulpprogramma, voegt u dat bestand toe aan een integratie account in Azure. Op die manier kan uw logische app toegang krijgen tot de meta gegevens van uw app wanneer u een 3270-connector actie toevoegt. De connector leest het meta gegevensbestand van uw integratie account, verwerkt de navigatie via de schermen van 3270 en geeft dynamisch de para meters voor de actie 3270 connector weer. U kunt vervolgens gegevens opgeven voor de hosttoepassing en de-connector retourneert de resultaten naar uw logische app. Op die manier kunt u uw oude apps integreren met Azure, micro soft en andere apps, services en systemen die Azure Logic Apps ondersteunt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aanbevolen: een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  U kunt deze omgeving selecteren als de locatie voor het maken en uitvoeren van uw logische app. Een ISE biedt toegang vanuit uw logische app tot resources die zijn beveiligd in azure Virtual Networks.

* De logische app die moet worden gebruikt voor het automatiseren en uitvoeren van uw op scherm gebaseerde app van 3270

  De IBM 3270-connector heeft geen triggers. Gebruik daarom een andere trigger om uw logische app te starten, zoals de trigger voor **terugkeer patronen** . U kunt vervolgens 3270-connector acties toevoegen. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om aan de slag te gaan. 
  Als u een ISE gebruikt, selecteert u die ISE als locatie van de logische app.

* [Down load en installeer het hulp programma 3270 design](https://aka.ms/3270-design-tool-download).
De enige vereiste is [Microsoft .NET Framework 4,8](https://aka.ms/net-framework-download).

  Dit hulp programma helpt u bij het vastleggen van de schermen, navigatie paden,-methoden en-para meters voor de taken in uw app die u toevoegt en uitvoert als 3270-connector acties. Het hulp programma genereert een HIDX-bestand (Host Integration Designer XML) met de benodigde meta gegevens voor de connector die moet worden gebruikt voor het aansturen van uw mainframe-app.
  
  Nadat u dit hulp programma hebt gedownload en geïnstalleerd, voert u de volgende stappen uit om verbinding te maken met uw host:

  1. Open het hulp programma 3270 design. Selecteer in het menu **sessie** de optie **sessies van hosts**.
  
  1. Geef de gegevens van de TN3270-host op.

* Een [integratie account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Dit is de locatie waar u uw HIDX-bestand opslaat als een kaart zodat uw logische app toegang heeft tot de meta gegevens en methode definities in dat bestand. 

  Zorg ervoor dat uw integratie account is gekoppeld aan de logische app die u gebruikt. Als u een ISE gebruikt, moet u er ook voor zorgen dat de locatie van uw integratie account dezelfde ISE is als de logische app.

* Toegang tot de TN3270-Server die als host fungeert voor uw mainframe-app

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Overzicht van meta gegevens maken

In een 3270 scherm-app zijn de schermen en gegevens velden uniek voor uw scenario's, zodat de 3270-connector deze informatie nodig heeft over uw app, die u als meta gegevens kunt opgeven. Deze meta gegevens bevatten informatie over het identificeren en herkennen van uw logische app. hierin wordt beschreven hoe u kunt navigeren tussen schermen, waar u gegevens kunt invoeren en waar u resultaten kunt verwachten. Als u deze meta gegevens wilt opgeven en genereren, gebruikt u het hulp programma 3270 design, dat u doorloopt in deze specifieke *modi*, of in fasen, zoals verderop in meer details wordt beschreven:

* **Vastleggen**: in deze modus legt u de schermen vast die nodig zijn voor het volt ooien van een specifieke taak met uw mainframe-app, zoals het ophalen van een bank saldo.

* **Navigatie**: in deze modus geeft u het plan of pad op voor het navigeren door de schermen van uw mainframe-app voor de specifieke taak.

* **Methoden**: in deze modus definieert u de methode, bijvoorbeeld, `GetBalance` die het pad naar het scherm navigatie beschrijft. U selecteert ook de velden op elk scherm die de invoer-en uitvoer parameters van de methode worden.

### <a name="unsupported-elements"></a>Niet-ondersteunde elementen

Het ontwerp hulpprogramma biedt geen ondersteuning voor deze elementen:

* Gedeeltelijke IBM Basic mapping-ondersteuning (BMS): als u een BMS-kaart importeert, negeert het ontwerp hulpprogramma gedeeltelijke scherm definities.

* Menu verwerking

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Schermen vastleggen

In deze modus markeert u een item op elk 3270 scherm waarmee het scherm uniek wordt geïdentificeerd. U kunt bijvoorbeeld een regel tekst of een complexere set voor waarden opgeven, zoals specifieke tekst en een niet-leeg veld. U kunt deze schermen opnemen via een live-verbinding met de hostserver, of deze gegevens importeren uit een BMS-kaart (IBM Basic mapping support). De live-verbinding maakt gebruik van een TN3270-emulator om verbinding te maken met de host. Elke connector actie moet worden toegewezen aan één taak die begint met het maken van verbinding met uw sessie en eindigt met het verbreken van de verbinding met uw sessie.

1. Als u dat nog niet hebt gedaan, opent u het hulp programma 3270 design. Selecteer **vastleggen** in de werk balk zodat u de modus vastleggen opgeeft.

1. Selecteer in het menu **sessie** de optie **verbinding maken**.

1. Als u de opname wilt starten, selecteert u in het menu **opnemen** de optie **opname starten**. (Toetsen bord: Ctrl + E)

1. In het deel venster **vastleggen** , vanaf het eerste scherm in uw app, moet u de app door lopen voor de specifieke taak die u wilt opnemen.

1. Nadat u de taak hebt voltooid, meldt u zich aan bij uw app.

1. Selecteer de **verbinding verbreken** in het menu van de **sessie** .

1. Als u de opname wilt stoppen, selecteert u **stoppen met opnemen** in het menu **opnemen** . (Toetsen bord: CTRL + SHIFT + E)

   Nadat u de schermen voor een taak hebt vastgelegd, ziet u in het hulp programma voor ontwerpen miniaturen die deze schermen vertegenwoordigen. Enkele opmerkingen over deze miniaturen:

   * In uw vastgelegde schermen bevindt zich een scherm met de naam ' empty '.

     Wanneer u voor het eerst verbinding maakt met [CICS](https://www.ibm.com/it-infrastructure/z/cics), moet u de sleutel ' Clear ' verzenden voordat u de naam kunt invoeren voor de trans actie die u wilt uitvoeren. Het scherm waarin u de sleutel ' Clear ' verzendt, heeft geen *herkennings kenmerken*, zoals een scherm titel, die u kunt toevoegen met behulp van de editor voor scherm herkenning. De miniaturen bevatten een scherm met de naam empty om dit scherm weer te geven. U kunt dit scherm later gebruiken voor het scherm waarin u de naam van de trans actie opgeeft.

   * De naam voor een vastgelegd scherm maakt standaard gebruik van het eerste woord op het scherm. Als deze naam al bestaat, wordt de naam met een onderstrepings teken en een nummer, bijvoorbeeld ' WBGB ' en ' WBGB_1 ', toegevoegd.

1. Voer de volgende stappen uit om een herken bare naam te geven aan een vastgelegd scherm:

   1. Selecteer in het deel venster **host schermen** het scherm waarvan u de naam wilt wijzigen.

   1. Ga in hetzelfde deel venster, onder aan de onderkant in hetzelfde deel venster, naar de eigenschap **scherm naam** .

   1. Wijzig de naam van het huidige scherm in een meer beschrijvende naam.

1. Geef nu de velden op voor het identificeren van elk scherm.

   Met de 3270-gegevens stroom hebben schermen geen standaard-id's, dus moet u op elk scherm unieke tekst selecteren. Voor complexe scenario's kunt u meerdere voor waarden opgeven, bijvoorbeeld unieke tekst en een veld met een specifieke voor waarde.

Wanneer u klaar bent met het selecteren van de herkennings velden, gaat u naar de volgende modus.

### <a name="conditions-for-identifying-repeated-screens"></a>Voor waarden voor het identificeren van herhaalde schermen

Als u de connector wilt navigeren en onderscheid wilt maken tussen schermen, zoekt u meestal unieke tekst op een scherm dat u kunt gebruiken als een id tussen de vastgelegde schermen. Voor herhaalde schermen hebt u mogelijk meer identificatie methoden nodig. Stel bijvoorbeeld dat u twee schermen hebt die er hetzelfde uitzien, met een scherm dat een geldige waarde retourneert, terwijl het andere scherm een fout bericht retourneert.

In het ontwerp programma kunt u *herkennings kenmerken* toevoegen, bijvoorbeeld een scherm titel zoals ' account saldo ophalen ', met behulp van de editor voor scherm herkenning. Als u een vervorkend pad hebt en beide vertakkingen hetzelfde scherm retour neren, maar met andere resultaten, hebt u andere herkennings kenmerken nodig. Tijdens runtime gebruikt de connector deze kenmerken voor het bepalen van de huidige vertakking en Fork. Dit zijn de voor waarden die u kunt gebruiken:

* Specifieke waarde: deze waarde komt overeen met de opgegeven teken reeks op de opgegeven locatie.
* GEEN specifieke waarde: deze waarde komt niet overeen met de opgegeven teken reeks op de opgegeven locatie.
* Empty: dit veld is leeg.
* Niet leeg: dit veld is niet leeg.

Zie het [Navigatie plan voor het voor beeld](#example-plan) verderop in dit onderwerp voor meer informatie.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Navigatie plannen definiëren

In deze modus definieert u de stroom of stappen voor het navigeren door de schermen van uw mainframe-app voor uw specifieke taak. Het kan bijvoorbeeld voor komen dat u meer dan één pad hebt dat uw app kan ondernemen, waarbij de ene pad het juiste resultaat geeft, terwijl het andere pad een fout veroorzaakt. Geef voor elk scherm de toetsaanslagen op die nodig zijn voor het verplaatsen naar het volgende scherm, zoals `CICSPROD <enter>` .

> [!TIP]
> Als u verschillende taken automatiseert die gebruikmaken van hetzelfde scherm verbinding maken en verbinding verbreken, biedt het ontwerp programma speciale plan typen voor verbinding maken en verbinding verbreken. Wanneer u deze plannen definieert, kunt u ze toevoegen aan het begin en het einde van het navigatie plan.

### <a name="guidelines-for-plan-definitions"></a>Richt lijnen voor plan definities

* Voeg alle schermen toe, beginnend met verbinding maken en eindigend met het verbreken van de verbinding.

* U kunt een zelfstandig plan maken of de abonnementen verbinding maken en verbreken gebruiken, waarmee u een reeks schermen kunt hergebruiken die gemeen schappelijk zijn voor al uw trans acties.

  * Het laatste scherm in het verbindings plan moet hetzelfde scherm zijn als het eerste scherm van het navigatie plan.

  * Het eerste scherm in uw verbrekings plan moet hetzelfde scherm zijn als het laatste scherm van het navigatie plan.

* Uw vastgelegde schermen kunnen veel herhaalde schermen bevatten, dus Selecteer en gebruik slechts één exemplaar van alle herhaalde schermen in uw plan. Hier volgen enkele voor beelden van herhaalde schermen:

  * Het aanmeldings scherm, bijvoorbeeld het scherm **bericht 10**
  * Het welkomst scherm voor CICS
  * Het scherm ' wissen ' of ' **leeg** '

<a name="create-plans"></a>

### <a name="create-plans"></a>Plannen maken

1. Selecteer op de werk balk van het 3270-ontwerp de optie **Navigatie** om de navigatie modus in te voeren.

1. Als u uw plan wilt starten, selecteert u in het **Navigatie** deel venster **nieuw plan**.

1. Voer onder **nieuwe plan naam kiezen** een naam in voor uw abonnement. Selecteer in de lijst **type** het plan type:

   | Plantype | Description |
   |-----------|-------------|
   | **Proces** | Voor zelfstandige of gecombineerde abonnementen |
   | **Verbinding maken** | Voor Connect-Abonnementen |
   | **Verbinding verbreken** | Voor het verbreken van abonnementen |
   |||

1. Sleep in het deel venster **host-schermen** de vastgelegde miniaturen naar het Opper vlak van het navigatie plan in het **Navigatie** deel venster.

   Gebruik het scherm ' leeg ' om het lege scherm weer te geven waarin u de naam van de trans actie opgeeft.

1. Rang Schik de schermen in de volg orde waarin de taak wordt beschreven die u definieert.

1. Als u het stroom traject tussen schermen wilt definiëren, inclusief vorken en samen voegingen, selecteert u in de werk balk van het ontwerp hulpmiddel **flow**.

1. Kies het eerste scherm in de stroom. Sleep en teken een verbinding met het volgende scherm in de stroom.

1. Geef voor elk scherm de waarden op voor de eigenschap van de **steun toets** (attentie-id) en voor de **vaste tekst** eigenschap, waarmee de stroom naar het volgende scherm wordt verplaatst.

   Mogelijk hebt u alleen de steun toets of de toets van de steun en de vaste tekst.

Nadat u het navigatie plan hebt voltooid, kunt u [de methoden in de volgende modus definiëren](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Voorbeeld

In dit voor beeld voert u een CICS-trans actie met de naam ' WBGB ' uit met de volgende stappen: 

* In het eerste scherm voert u een naam en een account in.
* In het tweede scherm krijgt u het account saldo.
* U verlaat het scherm ' leeg '.
* U meldt zich af bij CICS op het scherm MSG-10.

Stel dat u deze stappen herhaalt, maar u voert onjuiste gegevens in zodat u het scherm kunt vastleggen waarin de fout wordt weer gegeven. Dit zijn de schermen die u vastlegt:

* BERICHT-10
* CICS-Welkom
* Leeg
* WBGB_1 (invoer)
* WBGB_2 (fout)
* Empty_1
* MSG-10_1

Hoewel veel schermen hier unieke namen krijgen, zijn sommige schermen hetzelfde scherm, bijvoorbeeld ' MSG-10 ' en ' empty '. Voor een herhaald scherm gebruikt u slechts één exemplaar voor dat scherm in uw plan. Hier volgen enkele voor beelden die laten zien hoe een zelfstandig plan, verbindings plan, verbrekings plan en een gecombineerd plan eruitzien:

* Zelfstandig plan

  ![Zelfstandig navigatie plan](./media/connectors-create-api-3270/standalone-plan.png)

* Plan verbinden

  ![Plan verbinden](./media/connectors-create-api-3270/connect-plan.png)

* Abonnement verbreken

  ![Abonnement verbreken](./media/connectors-create-api-3270/disconnect-plan.png)

* Gecombineerd plan

  ![Gecombineerd plan](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Voor beeld: herhaalde schermen identificeren

Als u de connector wilt navigeren en onderscheid wilt maken tussen schermen, zoekt u meestal unieke tekst op een scherm dat u kunt gebruiken als een id in de vastgelegde schermen. Voor herhaalde schermen hebt u mogelijk meer identificatie methoden nodig. Het voorbeeld plan bevat een Fork waar u schermen kunt krijgen die er ongeveer uit zien. Het ene scherm retourneert een account saldo, terwijl het andere scherm een fout bericht retourneert.

Met het ontwerp hulpprogramma kunt u herkennings kenmerken toevoegen, bijvoorbeeld een scherm titel met de naam ' account saldo ophalen ', met behulp van de editor voor scherm herkenning. In het geval van vergelijk bare schermen hebt u andere kenmerken nodig. Tijdens runtime gebruikt de connector deze kenmerken om de vertakking en Fork te bepalen.

* In de vertakking die geldige invoer retourneert, het scherm met het account saldo, kunt u een veld met de voor waarde ' niet leeg ' toevoegen.

* In de vertakking die met een fout wordt geretourneerd, kunt u een veld toevoegen met de voor waarde ' empty '.

<a name="define-method"></a>

## <a name="define-methods"></a>Methoden definiëren

In deze modus definieert u een methode die is gekoppeld aan uw navigatie plan. Voor elke methode parameter geeft u het gegevens type op, zoals een teken reeks, een geheel getal, een datum of tijd, enzovoort. Wanneer u klaar bent, kunt u uw methode testen op de Live host en controleren of de methode werkt zoals verwacht. Vervolgens genereert u het meta gegevensbestand of het HIDX-bestand (Host Integration Designer), dat nu de methode definities bevat die moeten worden gebruikt voor het maken en uitvoeren van een actie voor de IBM 3270-connector.

1. Selecteer op de werk balk van het 3270-ontwerp de optie **methoden** om de modus methoden in te voeren. 

1. Selecteer in het **Navigatie** deel venster het scherm met de gewenste invoer velden.

1. Voer de volgende stappen uit om de eerste invoer parameter voor uw methode toe te voegen:

   1. Selecteer in het deel venster **vastleggen** in het scherm 3270-emulator het hele veld, niet alleen tekst in het veld, dat u als eerste invoer wilt.

      > [!TIP]
      > Om alle velden weer te geven en ervoor te zorgen dat u het veld voltooid selecteert, selecteert u in het menu **weer gave** **alle velden**.

   1. Selecteer **invoer veld** op de werk balk van het ontwerp hulpmiddel. 

   Herhaal de vorige stappen voor elke para meter om meer invoer parameters toe te voegen.

1. Voer de volgende stappen uit om de eerste uitvoer parameter voor uw methode toe te voegen:

   1. Selecteer in het deel venster **vastleggen** in het scherm 3270-emulator het hele veld, niet alleen tekst in het veld, dat u als de eerste uitvoer wilt.

      > [!TIP]
      > Om alle velden weer te geven en ervoor te zorgen dat u het veld voltooid selecteert, selecteert u in het menu **weer gave** **alle velden**.

   1. Op de werk balk van het ontwerp hulpmiddel selecteert u **uitvoer veld**.

   Herhaal de vorige stappen voor elke para meter om meer uitvoer parameters toe te voegen.

1. Nadat u alle para meters van uw methode hebt toegevoegd, definieert u deze eigenschappen voor elke para meter:

   | Naam van eigenschap | Mogelijke waarden | 
   |---------------|-----------------|
   | **Gegevens type** | Byte, datum en tijd, decimaal, int, lang, kort, teken reeks |
   | **De techniek voor veld opvulling** | Para meters ondersteunen deze typen opvullingen, waarbij zo nodig lege waarden worden gevuld: <p><p>- **Type**: Voer tekens opeenvolgend in het veld in. <p>- **Fill**: Vervang de inhoud van het veld door tekens en vul zo nodig lege waarden in. <p>- **EraseEofType**: wis het veld en voer de tekens vervolgens opeenvolgend in het veld in. |
   | **Teken reeks voor opmaak** | Sommige parameter gegevens typen gebruiken een indelings teken reeks, die de 3270-connector informeert de conversie van tekst van het scherm naar een .NET-gegevens type: <p><p>- **Datum tijd**: de notatie teken reeks voor [datum en tijd volgt de notatie teken reeksen voor .net-aangepaste datums en tijden](/dotnet/standard/base-types/custom-date-and-time-format-strings). De datum `06/30/2019` gebruikt bijvoorbeeld de notatie teken reeks `MM/dd/yyyy` . <p>- **Decimaal**: de teken reeks voor decimale notatie maakt gebruik van de [COBOL-component Picture](https://www.ibm.com/support/knowledgecenter/ssw_ibm_i_73/rzasb/picture.htm). Het getal `100.35` gebruikt bijvoorbeeld de notatie teken reeks `999V99` . |
   |||

## <a name="save-and-view-metadata"></a>Meta gegevens opslaan en weer geven

Nadat u uw methode hebt gedefinieerd, maar voordat u uw methode test, moet u alle gegevens die u tot nu toe hebt gedefinieerd, opslaan in een RAP-bestand (. rap).
U kunt op elk gewenst moment in een wille keurige modus opslaan naar dit RAP-bestand. Het ontwerp hulpprogramma bevat ook een voor beeld van een RAP-bestand dat u kunt openen en bekijken door te bladeren naar de installatiemap van het ontwerp hulpmiddel op deze locatie en het bestand ' WoodgroveBank. rap ' te openen:

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Als u echter probeert wijzigingen op te slaan in het RAP-voorbeeld bestand of een HIDX-bestand uit het RAP-voorbeeld bestand te genereren terwijl het bestand in de installatiemap van het ontwerp programma blijft, kan de fout ' toegang geweigerd ' worden weer geven. Het ontwerp programma wordt standaard geïnstalleerd in de map Program Files zonder verhoogde machtigingen. Als er een fout optreedt, kunt u een van de volgende oplossingen proberen:

* Kopieer het voorbeeld bestand naar een andere locatie.
* Voer het ontwerp hulpprogramma uit als beheerder.
* Maak uzelf de eigenaar van de SDK-map.

## <a name="test-your-method"></a>Uw methode testen

1. Als u uw methode op de Live host wilt uitvoeren, terwijl u nog steeds in de modus methoden, drukt u op de toets F5 of selecteert u op de werk balk van het ontwerp hulpmiddel **testen**.

   > [!TIP]
   > U kunt de modi op elk gewenst moment wijzigen. Selecteer in het menu **bestand** de optie **modus** en selecteer vervolgens de gewenste modus.

1. Voer de waarden van de para meters in en selecteer **OK**.

1. Selecteer **volgende** om door te gaan naar het volgende scherm.

1. Wanneer u klaar bent, selecteert u **gereed**, waarin de waarden van de uitvoer parameters worden weer gegeven.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>HIDX-bestand genereren en uploaden

Wanneer u klaar bent, genereert u het HIDX-bestand zodat u het kunt uploaden naar uw integratie account. Het 3270-ontwerp hulpprogramma maakt het HIDX-bestand in een nieuwe submap waar u het RAP-bestand hebt opgeslagen.

1. Selecteer in het hulp programma 3270-ontwerp de optie **definities genereren** in het menu **extra** . (Toetsen bord: F6)

1. Ga naar de map die het RAP-bestand bevat en open de submap die het hulp programma heeft gemaakt na het genereren van het HIDX-bestand. Controleer of het hulp programma het HIDX-bestand heeft gemaakt.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en zoek uw integratie account.

1. Voeg uw HIDX-bestand als een kaart toe aan uw integratie account door [deze vergelijk bare stappen te volgen voor het toevoegen van Maps](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), maar wanneer u het kaart type selecteert, selecteert u **HIDX**.

Verderop in dit onderwerp, wanneer u een IBM 3270-actie voor het eerst toevoegt aan uw logische app, wordt u gevraagd om een verbinding te maken tussen uw logische app en de hostserver door verbindings gegevens op te geven, zoals de namen voor uw integratie account en de hostserver. Nadat u de verbinding hebt gemaakt, kunt u het eerder toegevoegde HIDX-bestand, de methode die moet worden uitgevoerd, en de te gebruiken para meters selecteren.

Wanneer u al deze stappen hebt voltooid, kunt u de actie die u in uw logische app maakt, gebruiken om verbinding te maken met uw IBM-mainframe, schermen voor uw app, gegevens invoeren, resultaten retour neren, enzovoort. U kunt ook door gaan met het toevoegen van andere acties aan uw logische app voor de integratie met andere apps, services en systemen.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>IBM 3270-acties uitvoeren

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Selecteer **nieuwe stap** **>** **een actie toevoegen** onder de laatste stap waarin u een actie wilt toevoegen. 

1. Selecteer in het zoekvak **Enter prise**. Voer in het zoekvak `3270` als uw filter in. Selecteer in de lijst acties de actie met de naam **een mainframe programma uitvoeren via een TN3270-verbinding**

   ![Actie 3270 selecteren](./media/connectors-create-api-3270/select-3270-action.png)

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Als er nog geen verbinding bestaat, geeft u de benodigde informatie voor de verbinding op en selecteert u **maken**.

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Verbindingsnaam** | Ja | <*verbindings naam*> | De naam voor de verbinding |
   | **Integratie account-ID** | Yes | <*integratie-account-naam*> | De naam van uw integratie account |
   | **SAS-URL van integratie account** | Yes | <*integratie-account-SAS-URL*> | De URL van de Shared Access Signature (SAS) van uw integratie account, die u kunt genereren op basis van de instellingen van uw integratie account in de Azure Portal. <p>1. Selecteer in het menu van het integratie account onder **instellingen** de optie **URL voor terugbellen**. <br>2. Kopieer in het rechterdeel venster de waarde voor de **gegenereerde call back-URL** . |
   | **Server** | Yes | <*TN3270-Server naam*> | De server naam voor uw TN3270-service |
   | **Poort** | No | <*TN3270-Server-poort*> | De poort die wordt gebruikt door uw TN3270-Server. Als dit veld leeg blijft, wordt de connector `23` als de standaard waarde gebruikt. |
   | **Apparaattype** | No | <*IBM-Terminal model*> | De naam of het nummer van het model voor de IBM-Terminal dat moet worden geëmuleerd. Als dit veld leeg blijft, gebruikt de connector standaard waarden. |
   | **Codepagina** | No | <*code-pagina nummer*> | Het nummer van de code pagina voor de host. Als dit veld leeg blijft, wordt de connector `37` als de standaard waarde gebruikt. |
   | **Naam van logische eenheid** | No | <*naam van logische eenheid*> | De naam van de specifieke logische eenheid die moet worden aangevraagd vanaf de host |
   | **SSL inschakelen?** | No | In-of uitschakelen | Schakel TLS-code ring in of uit. |
   | **SSL-certificaat van host valideren?** | No | In-of uitschakelen | De validatie van het server certificaat in-of uitschakelen. |
   ||||

   Bijvoorbeeld:

   ![Verbindingseigenschappen](./media/connectors-create-api-3270/connection-properties.png)

1. Geef de benodigde informatie op voor de actie:

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Hidx-naam** | Yes | <*HIDX-bestands naam*> | Selecteer het 3270-HIDX-bestand dat u wilt gebruiken. |
   | **Methode naam** | Yes | <*methode-naam*> | Selecteer de methode in het HIDX-bestand dat u wilt gebruiken. Nadat u een methode hebt geselecteerd, wordt de lijst **nieuwe para meter toevoegen** weer gegeven, zodat u para meters kunt selecteren voor gebruik met deze methode. |
   ||||

   Bijvoorbeeld:

   **Het HIDX-bestand selecteren**

   ![HIDX-bestand selecteren](./media/connectors-create-api-3270/select-hidx-file.png)

   **De methode selecteren**

   ![Methode selecteren](./media/connectors-create-api-3270/select-method.png)

   **De para meters selecteren**

   ![Parameters selecteren](./media/connectors-create-api-3270/add-parameters.png)

1. Wanneer u klaar bent, slaat u uw logische app op en voert u deze uit.

   Nadat de uitvoering van de logische app is voltooid, worden de stappen van de uitvoering weer gegeven. 
   Geslaagde stappen laten zien hoe de letter ' X ' wordt weer gegeven.

1. Als u de invoer en uitvoer voor elke stap wilt bekijken, vouwt u deze stap uit.

1. Als u de uitvoer wilt bekijken, selecteert u **onbewerkte uitvoer weer geven**.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/si3270/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)

