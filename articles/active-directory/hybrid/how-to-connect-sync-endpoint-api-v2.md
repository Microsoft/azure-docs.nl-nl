---
title: V2-eind punt Azure AD Connect Sync | Microsoft Docs
description: Dit document bevat updates voor de API van Azure AD Connect-synchronisatie v2-eind punten.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0067ed618f95e7f83fc041fe214bbc4d715a504f
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567635"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Eindpunt-API voor Azure AD Connect-synchronisatie v2 
Micro soft heeft een nieuw eind punt (API) geïmplementeerd voor Azure AD Connect die de prestaties verbetert van de synchronisatie service-bewerkingen naar Azure Active Directory. Door gebruik te maken van het nieuwe v2-eind punt, kunt u zien dat er prestatie verhogingen optreden bij het exporteren en importeren naar Azure AD. Dit nieuwe eind punt ondersteunt het volgende:
    
 - groepen synchroniseren met Maxi maal 250.000 leden
 - prestatie verbeteringen bij het exporteren en importeren naar Azure AD
 
> [!NOTE]
> Op dit moment heeft het nieuwe eind punt geen geconfigureerde limiet voor de groeps grootte voor Microsoft 365 groepen die terug worden geschreven. Dit kan een invloed hebben op uw Active Directory en latentie van de synchronisatie cyclus. Het is raadzaam om de groeps grootten stapsgewijs te verg Roten.  

## <a name="prerequisites"></a>Vereisten  
Als u het nieuwe v2-eind punt wilt gebruiken, moet u [Azure AD Connect versie 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger gebruiken en de volgende implementatie stappen volgen om het v2-eind punt voor uw Azure AD Connect-server in te scha kelen.   

## <a name="deployment-guidance"></a>Implementatie richtlijnen 
U moet [Azure AD Connect versie 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger implementeren om het v2-eind punt te kunnen gebruiken. Gebruik de koppeling die u hebt ontvangen om te downloaden. 

Het is raadzaam om de [Swing migratie](./how-to-upgrade-previous-version.md#swing-migration) methode voor het implementeren van het nieuwe eind punt in uw omgeving uit te voeren. Dit zorgt voor een duidelijk onherstelbaar plan in het geval dat een belang rijke terugdraai actie nood zakelijk is. In het volgende voor beeld ziet u hoe een swing migratie in dit scenario kan worden gebruikt. Raadpleeg de meegeleverde koppeling voor meer informatie over de implementatie methode voor Swing migratie. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Swing migratie voor het implementeren van v2-eind punt
De volgende stappen begeleiden u bij het implementeren van het v2-eind punt met behulp van de swing-methode.

1. Implementeer het v2-eind punt op de huidige staging-server. Deze server wordt in de volgende stappen aangeduid als de **v2-server** . De huidige actieve server zal de werk belasting van de productie blijven verwerken met het v1-eind punt, die de **v1-server** hieronder wordt genoemd.
1. Controleer of de **v2-server** nog steeds import bewerkingen verwerkt zoals verwacht. In deze fase worden grote groepen niet ingericht voor Azure AD of on-premises AD, maar u kunt controleren of de upgrade niet tot andere onverwachte gevolgen voor het bestaande synchronisatie proces heeft geleid. 
2. Nadat de validatie is voltooid, schakelt u de **v2-server** als de actieve server en de **v1-server** als de staging-server. Op dit moment worden grote groepen die zich in de scope bevinden, ingericht voor Azure AD, en worden grote Microsoft 365 Unified groups ingericht voor AD, als groeps terugschrijven is ingeschakeld.
3. Controleer of de **v2-server** bezig is met het uitvoeren en verwerken van grote groepen. U kunt ervoor kiezen om in deze stap te blijven en het synchronisatie proces voor een bepaalde periode te controleren.
  >[!NOTE]
  > Als u wilt terugkeren naar de vorige configuratie, kunt u een swing migratie van de **v2-server** naar de **v1-server** uitvoeren. Omdat het v1-eind punt geen groepen ondersteunt met meer dan 50.000 leden, wordt een grote groep die is ingericht door Azure AD Connect, in azure AD of on-premises AD, vervolgens verwijderd. 
4. Wanneer u zeker weet dat u het v2-eind punt gebruikt, werkt u de **v1-server** bij om te beginnen met het gebruik van het v2-eind punt. 
 

## <a name="expectations-of-performance-impact"></a>Verwachtingen van invloed op de prestaties  
Bij het gebruik van het v2-eind punt zijn prestatie verbeteringen een functie van het aantal gesynchroniseerde groepen, de grootte van deze groepen en het verloop van de groep (de activiteit die het gevolg is van het toevoegen en verwijderen van gebruikers als leden van de groep). Als het nieuwe eind punt wordt gebruikt en het aantal, de grootte of het verloop van de gesynchroniseerde groepen niet groter wordt, moeten de export-en import bewerking in azure AD korter zijn. 
 
De prestatie verhogingen kunnen echter worden tenietgedaan door de extra verwerking die is vereist bij het synchroniseren van grote groepen. U kunt de totale synchronisatie tijd uiteindelijk verhogen door een te groot aantal grote groepen toe te voegen aan het synchronisatie proces.  

Om een beter inzicht te krijgen in de manier waarop de toevoeging van de nieuwe groepen van invloed is op de synchronisatie prestaties, is het raadzaam dat u eerst slechts enkele grote groepen met minder dan 100.000 leden synchroniseert. U kunt vervolgens het aantal en de grootte van groepen verhogen door meer van deze te bereiken, via OE, kenmerk of Max. filteren van groeps grootte. De prestatie verbeteringen worden gerealiseerd op de export-en import taken voor de Azure AD-connector, niet op de on-premises AD-connector. 

## <a name="deployment-step-by-step"></a>Implementatie stap per stap 
De volgende drie fasen zijn een uitgebreid voor beeld van het implementeren van het nieuwe v2-eind punt.  Gebruik de fasen als richt lijn voor uw implementatie.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fase 1: Azure AD Connect installeren en valideren 
Het is raadzaam om eerst de stappen voor het installeren of upgraden van [Azure AD Connect versie 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger uit te voeren en het synchronisatie proces te valideren voordat u naar de tweede fase gaat, waar u het v2-eind punt inschakelt. Op de Azure AD Connect-server: 


1. Beschrijving Database back-up maken 
2. Installeer of upgrade naar [Azure AD Connect versie 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger.
3. De installatie valideren 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fase 2: het v2-eind punt inschakelen 
De volgende stap is het v2-eind punt in te scha kelen. 

> [!NOTE]
> Nadat u het v2-eind punt voor uw server hebt ingeschakeld, kunt u de prestatie verbeteringen voor uw bestaande werk belasting bekijken. U kunt ook geen groepen synchroniseren met meer 50.000-leden. 

Als u wilt overschakelen naar het v2-eind punt, gebruikt u de volgende stappen: 

1. Open een Power shell-prompt als beheerder. 
2. Schakel de synchronisatie planner uit nadat u hebt gecontroleerd of er geen synchronisatie bewerkingen worden uitgevoerd: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. De nieuwe module importeren: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Overschakelen naar het v2-eind punt:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
U hebt nu het v2-eind punt voor uw server ingeschakeld. Neem even de tijd om te controleren of er geen onverwachte resultaten zijn nadat u het v2-eind punt hebt ingeschakeld voordat u naar de volgende fase gaat, waarbij u de limiet voor de groeps grootte verhoogt. 
>[!NOTE]
>Het pad naar de bestands-of module kan een andere stationsletter gebruiken, afhankelijk van het installatiepad dat bij de installatie van Azure AD Connect wordt gegeven. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fase 3: de limiet voor groepslid maatschappen verhogen 
Nadat u hebt gecontroleerd of de service wordt uitgevoerd zonder onverwachte resultaten, kunt u door gaan met het verhogen van de limiet voor groepslid maatschappen. Het is raadzaam om eerst de lidmaatschaps limiet te verhogen naar een iets hogere waarde, bijvoorbeeld e g. 75K-leden voor een overzicht van de grotere groepen die worden gesynchroniseerd met Azure AD. Zodra u tevreden bent met de resultaten, kunt u de limiet voor leden verder verhogen.  

De maximum limiet is 250.000 leden per groep. 

De volgende stappen kunnen worden gebruikt om de limiet voor lidmaatschappen te verhogen:  

1. Editor voor synchronisatie regels van Azure AD openen 
2. Kies **uitgaande** voor richting in de editor 
3. Klik op de regel **voor het samen voegen van de groep aan Aad** 
4. Klik op  de ![ scherm opname van de knop bewerken die de selectie vakjes voor het weer geven en beheren van uw synchronisatie regels ' met ' uit voor Aad-groeps koppeling ' is geselecteerd.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Klik op de knop **Ja** om de standaard regel uit te scha kelen en een Bewerk bare kopie te maken.
 ![Scherm opname van het venster ' bevestiging van de gereserveerde regel bewerken ' met de knop Ja geselecteerd.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. In het pop-upvenster op de pagina **Beschrijving** stelt u de prioriteit in op een beschik bare waarde tussen 1 en 99 ![ scherm afbeelding met het venster ' regel voor uitgaande synchronisatie bewerken ' met ' prioriteit ' gemarkeerd.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Werk op de pagina trans **formaties** de **bron** waarde voor de **leden** transformatie bij en vervang ' 50000 ' door een waarde tussen 50001 en 250000. Met deze vervanging wordt de maximale grootte van het lidmaatschap van groepen die worden gesynchroniseerd met Azure AD verg root. We raden aan om te beginnen met een aantal 100.000 om inzicht te krijgen in de gevolgen die het synchroniseren van grote groepen op de synchronisatie prestaties heeft. 
 
 **Voorbeeld** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Synch regel bewerken](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Klik op Opslaan 
10. Power shell-prompt voor beheerders openen 
11. De synchronisatie planner opnieuw inschakelen 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Als Azure AD Connect Health niet is ingeschakeld, wijzigt u de instellingen van het Windows-toepassings gebeurtenis logboek om de logboeken te archiveren, in plaats van deze te overschrijven. De logboeken kunnen worden gebruikt om te helpen bij toekomstige probleemoplossings taken. 

>[!NOTE]
> Nadat het nieuwe eind punt is ingeschakeld, ziet u mogelijk extra export fouten op de AAD-connector met de naam DN-Attributes-failure. Er wordt een vermelding in het gebeurtenis logboek voor elke fout weer gegeven met id 6949. De fouten zijn informatie en geven geen problemen met uw installatie op, maar in plaats daarvan kan het synchronisatie proces bepaalde leden niet toevoegen aan een groep in azure AD omdat het lidobject zelf niet is gesynchroniseerd met Azure AD. 

De nieuwe v2-eindpunt code verwerkt sommige typen export fouten die iets afwijkt van de manier waarop de V1-code had.  Mogelijk worden er meer informatieve fout berichten weer geven wanneer u het v2-eind punt gebruikt. 

>[!NOTE]
> Bij het upgraden van Azure AD Connect moet u ervoor zorgen dat de stappen in fase 2 opnieuw worden uitgevoerd, omdat de wijzigingen niet worden bewaard tijdens het upgrade proces. 

Tijdens de volgende toename van de limiet voor groepslid maatschap in de regel **voor het samen voegen van Aad-groeps koppelingen** is een volledige synchronisatie niet nodig, dus u kunt ervoor kiezen om de volledige synchronisatie te onderdrukken door de volgende opdracht in Power shell uit te voeren. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Als u Microsoft 365 Unified Groups hebt die meer dan 50.000 leden hebben, worden de groepen in Azure AD Connect gelezen en als groeps terugschrijven is ingeschakeld, worden deze naar uw on-premises AD-locatie geschreven. 

## <a name="rollback"></a>Terugdraaiactie 
Als u het v2-eind punt hebt ingeschakeld en wilt terugdraaien, voert u de volgende stappen uit: 

1. Op de Azure AD Connect-server: a. Beschrijving Database back-up maken 
2. Een Power shell-prompt voor beheerders openen:
3. Synchronisatie planner uitschakelen na het controleren of er geen synchronisatie bewerkingen worden uitgevoerd
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Overschakelen naar het v1-eind punt * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Editor voor synchronisatie regels van Azure AD openen 
6. Verwijder de Bewerk bare kopie van de regel **voor het samen voegen van Aad-groepslidlijke koppelingen** 
7. Schakel de standaard kopie van de regel **voor het samen voegen van Aad-groepslid** maatschap in 
8. Een Power shell-prompt voor beheerders openen 
9. De synchronisatie planner opnieuw inschakelen 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Wanneer u terugschakelt van de v2 naar v1-eind punten, worden groepen die zijn gesynchroniseerd met meer dan 50.000 leden verwijderd nadat een volledige synchronisatie is uitgevoerd, voor zowel AD-groepen die zijn ingericht voor Azure AD als Microsoft 365 Unified groups die aan AD zijn ingericht. 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen  
 
**Wanneer wordt het nieuwe eind punt de standaard waarde voor upgrades en nieuwe installaties?**  
</br>Er wordt een nieuwe release van AADConnect gepland die in januari 2021 moet worden gepubliceerd om te worden gedownload. Deze versie maakt standaard gebruik van het v2-eind punt en schakelt synchronisatie groepen in die groter zijn dan 50.000 zonder extra configuratie. Deze release wordt vervolgens gepubliceerd voor automatische upgrade naar servers die in aanmerking komen.
 
## <a name="next-steps"></a>Volgende stappen

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
