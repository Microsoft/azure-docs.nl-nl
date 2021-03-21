---
title: Problemen oplossen met een object dat niet wordt gesynchroniseerd met Azure Active Directory | Microsoft Docs '
description: Problemen oplossen met een object dat niet wordt gesynchroniseerd met Azure Active Directory.
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
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94413459"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Problemen oplossen met een object dat niet synchroniseert met Azure Active Directory

Als een object niet op de verwachte manier wordt gesynchroniseerd met Microsoft Azure Active Directory (Azure AD), kan dit verschillende oorzaken hebben. Als u een e-mail bericht van Azure AD hebt ontvangen of als u de fout in Azure AD Connect Health ziet, lees dan in plaats daarvan [problemen oplossen tijdens de synchronisatie](tshoot-connect-sync-errors.md) . Maar als u een probleem oplost waarbij het object zich niet in azure AD bevindt, is dit artikel voor u. Hierin wordt beschreven hoe u fouten kunt vinden in het on-premises onderdeel Azure AD Connect synchronisatie.

>[!IMPORTANT]
>Voor Azure AD Connect-implementatie met versie 1.1.749.0 of hoger gebruikt u de [taak probleem oplossing](tshoot-connect-objectsync.md) in de wizard om problemen met het synchroniseren van objecten op te lossen. 

## <a name="synchronization-process"></a>Synchronisatie proces

Voordat we synchronisatie problemen onderzoeken, begrijpen we het Azure AD Connect synchronisatie proces:

  ![Diagram van Azure AD Connect synchronisatie proces](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Connector ruimte, een tabel in een Data Base
* **Mv:** Omgekeerde, een tabel in een Data Base

### <a name="synchronization-steps"></a>**Synchronisatie stappen**
Het synchronisatie proces bestaat uit de volgende stappen:

1. **Importeren uit AD:** Active Directory objecten worden in de Active Directory CS gebracht.

2. **Importeren vanuit Azure AD:** Azure AD-objecten worden binnengebracht in azure AD CS.

3. **Synchronisatie:** Binnenkomende synchronisatie regels en regels voor uitgaande synchronisatie worden uitgevoerd in volg orde van prioriteits nummer, van lager naar hoger. Als u de synchronisatie regels wilt weer geven, gaat u naar de editor voor synchronisatie regels van de bureaublad toepassingen. De binnenkomende synchronisatie regels brengen gegevens over van CS naar MV. Met de regels voor uitgaande synchronisatie worden gegevens verplaatst van MV naar CS.

4. **Exporteren naar AD:** Na het synchroniseren worden objecten uit de Active Directory-CS geëxporteerd naar Active Directory.

5. **Exporteren naar Azure AD:** Na het synchroniseren worden objecten van Azure AD CS geëxporteerd naar Azure AD.

## <a name="troubleshooting"></a>Problemen oplossen

Als u de fouten wilt vinden, bekijkt u een aantal verschillende locaties in de volgende volg orde:

1. De [bewerkings logboeken](#operations) voor het vinden van fouten die zijn geïdentificeerd door de synchronisatie-engine tijdens het importeren en synchroniseren.
2. De [connector ruimte](#connector-space-object-properties) om ontbrekende objecten en synchronisatie fouten te vinden.
3. De [tekst](#metaverse-object-properties) die te maken heeft met gegevens problemen.

Start [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) voordat u begint met deze stappen.

## <a name="operations"></a>Operations
Op het tabblad **bewerkingen** in Synchronization Service Manager kunt u beginnen met het oplossen van problemen. Op dit tabblad worden de resultaten van de meest recente bewerkingen weer gegeven. 

![Scherm afbeelding van Synchronization Service Manager, tabblad weer geven van bewerkingen](./media/tshoot-connect-object-not-syncing/operations.png)  

In het bovenste gedeelte van het tabblad **bewerkingen** worden alle uitvoeringen in chronologische volg orde weer gegeven. Standaard houdt het operations-logboek informatie over de afgelopen zeven dagen, maar deze instelling kan worden gewijzigd met de [scheduler](how-to-connect-sync-feature-scheduler.md). Zoek naar een wille keurige uitvoering die geen **succes** status weergeeft. U kunt de sorteer volgorde wijzigen door te klikken op de kopteksten.

De kolom **status** bevat de belangrijkste informatie en toont het ernstigste probleem voor een uitvoering. Hier volgt een korte samen vatting van de meest voorkomende statussen in volg orde van prioriteit van onderzoek (waarbij * duidt op meerdere mogelijke fout teken reeksen).

| Status | Opmerking |
| --- | --- |
| gestopt-* |De uitvoering kan niet worden voltooid. Dit kan bijvoorbeeld gebeuren als het externe systeem niet actief is en er geen contact kan worden opgenomen. |
| gestopt-fout limiet |Er zijn meer dan 5.000 fouten. De uitvoering is automatisch gestopt vanwege het grote aantal fouten. |
| voltooid- \* -fouten |De uitvoering is voltooid, maar er zijn fouten (minder dan 5.000) die moeten worden onderzocht. |
| voltooid- \* -waarschuwingen |De uitvoering is voltooid, maar sommige gegevens hebben niet de verwachte status. Als u fouten hebt, is dit bericht meestal alleen een symptoom. Onderzoek pas waarschuwingen als u fouten hebt opgelost. |
| voltooid |Geen problemen. |

Wanneer u een rij selecteert, wordt de onderkant van het tabblad **bewerkingen** bijgewerkt, zodat de details van die uitvoering worden weer gegeven. Aan de linkerkant van dit gebied hebt u mogelijk een lijst met de titel **stap #**. Deze lijst wordt alleen weer gegeven als u meerdere domeinen in uw forest hebt en elk domein wordt vertegenwoordigd door een stap. De domein naam kan worden gevonden onder de koptekst **partitie**. Onder de kop **synchronisatie statistieken** vindt u meer informatie over het aantal wijzigingen dat is verwerkt. Selecteer de koppelingen om een lijst met gewijzigde objecten op te halen. Als u objecten met fouten hebt, worden deze fouten weer gegeven onder de kop **synchronisatie fouten** .

### <a name="errors-on-the-operations-tab"></a>Fouten op het tabblad bewerkingen
Wanneer u fouten hebt, Synchronization Service Manager het object in de fout weer gegeven en de fout zelf als koppelingen die meer informatie geven.

![Scherm opname van fouten in Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Begin met het selecteren van de fout teken reeks. (In de voor gaande afbeelding is de fout reeks **Sync-regel-error-function-geactiveerd**.) U krijgt eerst een overzicht van het object. Als u de werkelijke fout wilt zien, selecteert u **Stack tracering**. Deze tracering biedt informatie over debugniveau voor de fout.

Klik met de rechter muisknop op het vak **informatie over de aanroep stack** , klik op **Alles selecteren** en selecteer vervolgens **kopiëren**. Kopieer vervolgens de stack en Bekijk de fout in uw favoriete editor, zoals Klad blok.

Als de fout afkomstig is uit **SyncRulesEngine**, worden in de stack gegevens voor de aanroep eerst alle kenmerken van het object weer gegeven. Schuif omlaag totdat u de kop **InnerException =>** ziet.  

  ![Scherm afbeelding van de Synchronization Service Manager, met de fout informatie onder de kop InnerException =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
De regel na de kop toont de fout. In de vorige afbeelding is de fout afkomstig uit een aangepaste synchronisatie regel die door Fabrikam is gemaakt.

Als de fout niet voldoende informatie geeft, is het tijd om de gegevens zelf te bekijken. Selecteer de koppeling met de object-id en ga door met het oplossen van problemen met het [geïmporteerde object in de connector ruimte](#cs-import).

## <a name="connector-space-object-properties"></a>Eigenschappen van het object van het connectorgebied
Als op het tabblad [**bewerkingen**](#operations) geen fouten worden weer gegeven, volgt u het connector Space-object van Active Directory naar het omgekeerde naar Azure AD. In dit pad moet u vinden waar het probleem zich bevindt.

### <a name="searching-for-an-object-in-the-cs"></a>Zoeken naar een object in de CS

Selecteer in Synchronization Service Manager **connectors**, selecteer de Active Directory-connector en selecteer **ruimte voor Zoek connector**.

Selecteer in het vak **bereik** **RDN** wanneer u wilt zoeken op het kenmerk CN of selecteer **DN of anker** wanneer u wilt zoeken op het kenmerk **DN** -naam. Voer een waarde in en selecteer **zoeken**. 
 
![Scherm afbeelding van een zoek opdracht voor een connector ruimte](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Als u het object dat u zoekt niet kunt vinden, is het mogelijk gefilterd met filteren [op domein basis](how-to-connect-sync-configure-filtering.md#domain-based-filtering) of [filteren op basis](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)van een organisatie-eenheid. Als u wilt controleren of het filter is geconfigureerd zoals verwacht, lees dan [Azure AD Connect Sync: Configure filtering](how-to-connect-sync-configure-filtering.md).

U kunt een andere nuttige zoek opdracht uitvoeren door de Azure AD-connector te selecteren. Selecteer in het vak **bereik** de optie **in wachtrij worden geïmporteerd** en schakel vervolgens het selectie vakje **toevoegen** in. Deze zoek opdracht geeft u alle gesynchroniseerde objecten in azure AD die niet aan een on-premises object kunnen worden gekoppeld.  

![Scherm afbeelding van zwevende ruimten in een zoek opdracht voor een connector](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Deze objecten zijn gemaakt door een andere synchronisatie-engine of een synchronisatie-engine met een andere filter configuratie. Deze zwevende objecten worden niet meer beheerd. Bekijk deze lijst en overweeg deze objecten te verwijderen met behulp van de [Azure AD Power shell](/previous-versions/azure/jj151815(v=azure.100)) -cmdlets.

### <a name="cs-import"></a>CS importeren
Wanneer u een CS-object opent, zijn er verschillende tabbladen bovenaan. Op het tabblad **importeren** worden de gegevens weer gegeven die zijn klaargezet na het importeren.  

![Scherm afbeelding van het object voor de verbindings ruimte venster Eigenschappen, waarbij het tabblad importeren is geselecteerd](./media/tshoot-connect-object-not-syncing/csobject.png)    

In de kolom **oude waarde** wordt weer gegeven wat op dit moment wordt opgeslagen in Connect. in de kolom **nieuwe waarde** wordt weer gegeven wat er is ontvangen van het bron systeem en nog niet is toegepast. Als er een fout optreedt op het object, worden de wijzigingen niet verwerkt.

Het tabblad **synchronisatie fout** is alleen zichtbaar in het venster **Eigenschappen van connector ruimte-object** als er een probleem is met het object. Lees voor meer informatie over [het oplossen van synchronisatie fouten op het tabblad **bewerkingen**](#errors-on-the-operations-tab).

![Scherm afbeelding van het tabblad Synchronisatie fout in de verbindings ruimte-object venster Eigenschappen](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS-afkomst
Op het tabblad **afkomst** in het venster **Eigenschappen van connector ruimte object** ziet u hoe het object voor de connector ruimte aan het omgekeerde object is gerelateerd. U kunt zien wanneer de connector voor het laatst een wijziging van het verbonden systeem heeft geïmporteerd en welke regels worden toegepast om gegevens in de tekst te vullen.  

![Scherm opname van het afkomst-tabblad in het object connector ruimte venster Eigenschappen](./media/tshoot-connect-object-not-syncing/cslineage.png)  

In de voor gaande afbeelding toont de kolom **actie** een regel voor binnenkomende synchronisatie met de actie- **inrichting**. Dit geeft aan dat, zolang dit connector ruimte-object aanwezig is, het omgekeerde object blijft. Als in de lijst met synchronisatie regels een regel voor uitgaande synchronisatie met een **inrichtings** actie wordt weer gegeven, wordt dit object verwijderd wanneer het omgekeerde object wordt verwijderd.  

![Scherm opname van een afkomst-venster op het tabblad afkomst in het object connector Space venster Eigenschappen](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

In de voor gaande afbeelding ziet u ook in de kolom **PasswordSync** dat de binnenkomende connector ruimte kan bijdragen aan het wacht woord, omdat één synchronisatie regel de waarde **waar** heeft. Dit wacht woord wordt via de regel voor uitgaande verbindingen verzonden naar Azure AD.

Op het tabblad **afkomst** kunt u naar de omgekeerde tekst gaan door de eigenschappen van het [**omgekeerde object**](#mv-attributes)te selecteren.

### <a name="preview"></a>Preview
In de linkerbenedenhoek van het venster Eigenschappen van **connector ruimte object** ziet u de knop **Preview** . Selecteer deze knop om de **voorbeeld** pagina te openen, waar u één object kunt synchroniseren. Deze pagina is handig als u problemen met bepaalde aangepaste synchronisatie regels oplost en het effect van een wijziging op een enkel object wilt bekijken. U kunt een **volledige synchronisatie** of een **Delta synchronisatie** selecteren. U kunt ook **Preview genereren** selecteren, waarbij alleen de wijziging in het geheugen wordt bewaard. Of selecteer **Doorvoervoorbeeld**, waarmee de mailverse wordt bijgewerkt en alle wijzigingen in doel connector ruimten worden doorgevoerd.  

![Scherm opname van de pagina voor beeld, met voor beeld van starten geselecteerd](./media/tshoot-connect-object-not-syncing/preview.png)  

In het voor beeld kunt u het object controleren en zien welke regel wordt toegepast op een bepaalde kenmerk stroom.  

![Scherm afbeelding van de voorbeeld pagina, met de kenmerk stroom importeren](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Logboek
Selecteer naast de knop **voor beeld** de knop **logboek** om de **logboek** pagina te openen. Hier ziet u de status en geschiedenis van de wachtwoord synchronisatie. Zie problemen met wachtwoord- [hash-synchronisatie met Azure AD Connect synchronisatie oplossen](tshoot-connect-password-hash-synchronization.md)voor meer informatie.

## <a name="metaverse-object-properties"></a>Eigenschappen van het omgekeerde object
Het is doorgaans beter om te zoeken vanaf de bron Active Directory-Connector ruimte. Maar u kunt ook beginnen met zoeken vanuit de tekst.

### <a name="searching-for-an-object-in-the-mv"></a>Zoeken naar een object in de MV
Selecteer in Synchronization Service Manager de optie **omgekeerde zoek opdracht**, zoals in de volgende afbeelding. Een query maken waarvan u weet dat deze de gebruiker heeft gevonden. Zoek naar algemene kenmerken, zoals **AccountName** (**SAMAccountName**) en **userPrincipalName**. Zie [Sync Service Manager omgekeerde zoek opdracht](how-to-connect-sync-service-manager-ui-mvsearch.md)voor meer informatie.

![Scherm opname van Synchronization Service Manager, met het geselecteerde tabblad voor het zoeken van tekst](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Klik in het venster **Zoek resultaten** op het object.

Als u het object niet hebt gevonden, is het nog niet bereikt. Ga door met zoeken naar het object in de [ruimte](#connector-space-object-properties)van de Active Directory-connector. Als u het object in de ruimte van de Active Directory-connector vindt, kan er een synchronisatie fout optreden waardoor het object niet meer naar de tekst kan worden verzonden of een filter voor het bereik van de synchronisatie regel kan worden toegepast.

### <a name="object-not-found-in-the-mv"></a>Object niet gevonden in de MV
Als het object zich in de Active Directory-CS bevindt, maar niet aanwezig is in de MV, wordt een bereik filter toegepast. Als u het filter bereik wilt bekijken, gaat u naar het menu bureaublad toepassing en selecteert u **Editor voor synchronisatie regels**. Filter de regels die van toepassing zijn op het object door het filter hieronder aan te passen.

  ![Scherm afbeelding van de editor voor synchronisatie regels, met een zoek opdracht voor inkomende synchronisatie regels](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Bekijk elke regel in de bovenstaande lijst en controleer het **filter bereik**. Als de waarde van **isCriticalSystemObject** in het volgende bereik is ingesteld op null of False of leeg, wordt deze in de scope.

  ![Scherm afbeelding van het filter bereik in een zoek opdracht voor een inkomende synchronisatie regel](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Ga naar de [CS](#cs-import) -lijst met import kenmerken en controleer welk filter het object blokkeert van verplaatsing naar de MV. In de lijst met kenmerken van de **connector ruimte** worden alleen niet-null-en niet-lege kenmerken weer gegeven. Als **isCriticalSystemObject** bijvoorbeeld niet in de lijst wordt weer gegeven, is de waarde van dit kenmerk null of leeg.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Object niet gevonden in de Azure AD-CS
Als het object niet aanwezig is in de connector ruimte van Azure AD, maar wel aanwezig is in de MV, kijkt u naar het bereik filter van de uitgaande regels van de corresponderende connector ruimte en gaat u na of het object wordt gefilterd omdat de [kenmerken van MV](#mv-attributes) niet voldoen aan de criteria.

Als u het filter voor uitgaande scoping wilt bekijken, selecteert u de toepasselijke regels voor het object door het filter hieronder aan te passen. Bekijk elke regel en Bekijk de overeenkomende waarde voor het [kenmerk MV](#mv-attributes) .

  ![Scherm opname van een zoek opdracht voor uitgaande synchronisatie regels in de editor voor synchronisatie regels](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV kenmerken
Op het tabblad **kenmerken** ziet u de waarden en welke Connect oren ze hebben bijgedragen.  

![Scherm afbeelding van het omgekeerde object venster Eigenschappen, met het tabblad kenmerken geselecteerd](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Als een object niet wordt gesynchroniseerd, vraagt u de volgende vragen over kenmerk toestanden in het omgekeerde:
- Is het kenmerk **cloudFiltered** aanwezig en ingesteld op **waar**? Als dit het geval is, is dit gefilterd op basis van de stappen in [op kenmerken gebaseerde filtering](how-to-connect-sync-configure-filtering.md#attribute-based-filtering).
- Is het kenmerk **Source Anchor** aanwezig? Als dat niet het geval is, hebt u een account-resource forest-topologie? Als een object wordt geïdentificeerd als een gekoppeld postvak (het kenmerk **msExchRecipientTypeDetails** heeft de waarde **2**), wordt de **Source Anchor** bijgedragen door het forest met een ingeschakeld Active Directory-account. Zorg ervoor dat het hoofd account is geïmporteerd en correct is gesynchroniseerd. De hoofd account moet worden weer gegeven tussen de [connectors](#mv-connectors) voor het object.

### <a name="mv-connectors"></a>MV-connectors
Op het tabblad **connectors** worden alle connector ruimten weer gegeven die een representatie van het object hebben. 
 
![Scherm afbeelding van het omgekeerde object venster Eigenschappen, met het tabblad connectors geselecteerd](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

U moet een connector hebben voor het volgende:

- Elk Active Directory forest waarin de gebruiker wordt weer gegeven. Deze representatie kan **foreignSecurityPrincipals** en **contact** objecten bevatten.
- Een connector in azure AD.

Als de connector ontbreekt in azure AD, raadpleegt u de sectie over [MV Attributes](#mv-attributes) om de criteria voor het inrichten van Azure ad te controleren.

Op het tabblad **connectors** kunt u ook naar het [object connector ruimte](#connector-space-object-properties)gaan. Selecteer een rij en klik op **Eigenschappen**.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md).
- Meer informatie over [hybride identiteit](whatis-hybrid-identity.md).