---
title: Azure AD Connect V2-eindpuntsynchronisatie | Microsoft Docs
description: Dit document bevat informatie over updates voor de API voor Azure AD Connect-synchronisatie v2-eindpunten.
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
ms.openlocfilehash: 4434b59044aed8c9814431864e5c3c9b7d98254c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575714"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Eindpunt-API voor Azure AD Connect-synchronisatie v2 
Microsoft heeft een nieuw eindpunt (API) geïmplementeerd voor Azure AD Connect die de prestaties van de synchronisatieservicebewerkingen voor de Azure Active Directory. Door het nieuwe V2-eindpunt te gebruiken, zult u merkbare prestatieverbeteringen ervaren bij het exporteren en importeren naar Azure AD. Dit nieuwe eindpunt ondersteunt het volgende:
    
 - groepen synchroniseren met maximaal 250.000 leden
 - prestatieverbeteringen bij exporteren en importeren in Azure AD
 
> [!NOTE]
> Op dit moment heeft het nieuwe eindpunt geen geconfigureerde groepsgroottelimiet voor Microsoft 365 groepen die worden teruggeschreven. Dit kan van invloed zijn op de latentie van uw Active Directory- en synchronisatiecyclus. Het is raadzaam om de groepsgrootten stapsgewijs te vergroten.  

>[!NOTE]
> De Azure AD Connect sync V2-eindpunt-API is momenteel alleen beschikbaar in deze Azure-omgevingen:
> - Azure Commercial
> - Azure China-cloud
> - Azure US Government-cloud Deze wordt niet beschikbaar gesteld in de Duitse Azure-cloud

## <a name="prerequisites"></a>Vereisten  
Als u het nieuwe V2-eindpunt wilt gebruiken, moet u Azure AD Connect versie [1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger gebruiken en de onderstaande implementatiestappen volgen om het V2-eindpunt voor uw Azure AD Connect-server in te Azure AD Connect.   

## <a name="deployment-guidance"></a>Implementatie-richtlijnen 
U moet versie [Azure AD Connect versie 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger implementeren om het V2-eindpunt te gebruiken. Gebruik de meegeleverde koppeling om te downloaden. 

U wordt aangeraden de [swingmigratiemethode te volgen](./how-to-upgrade-previous-version.md#swing-migration) voor het uitrollen van het nieuwe eindpunt in uw omgeving. Dit biedt een duidelijk plan voor onvoorziene gebeurtenissen in het geval dat een grote terugdraaien noodzakelijk is. In het volgende voorbeeld ziet u hoe een swingmigratie in dit scenario kan worden gebruikt. Raadpleeg de koppeling voor meer informatie over de implementatiemethode voor swingmigratie. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Swingmigratie voor het implementeren van V2-eindpunt
De volgende stappen helpen u bij het implementeren van het v2-eindpunt met behulp van de swingmethode.

1. Implementeer het V2-eindpunt op de huidige faseringsserver. Deze server wordt in de onderstaande stappen de **V2-server** genoemd. De huidige actieve server blijft de productieworkload verwerken met behulp van het V1-eindpunt, dat de **onderstaande V1-server wordt** genoemd.
1. Controleer of de **V2-server** nog steeds importbewerkingen verwerkt zoals verwacht. In deze fase worden grote groepen niet ingericht voor Azure AD of on-prem AD, maar u kunt wel controleren of de upgrade geen andere onverwachte gevolgen heeft gehad voor het bestaande synchronisatieproces. 
2. Zodra de validatie is voltooid, schakelt u **de V2-server** over naar de actieve server en de **V1-server** als de faseringsserver. Op dit moment worden grote groepen die binnen het bereik vallen om te worden gesynchroniseerd, ingericht voor Azure AD, en grote Microsoft 365 geïntegreerde groepen worden ingericht voor AD als terugschrijven van groepen is ingeschakeld.
3. Controleer of de **V2-server** grote groepen goed presteert en verwerkt. U kunt ervoor kiezen om bij deze stap te blijven en het synchronisatieproces een bepaalde periode te bewaken.
  >[!NOTE]
  > Als u wilt teruggaan naar uw vorige configuratie, kunt u een swingmigratie uitvoeren van de **V2-server** naar **de V1-server.** Omdat het V1-eindpunt geen ondersteuning biedt voor groepen met meer dan 50.000 leden, wordt elke grote groep die is ingericht door Azure AD Connect, in Azure AD of on-prem AD, vervolgens verwijderd. 
4. Wanneer u zeker weet dat u het V2-eindpunt gebruikt, moet u de **V1-server upgraden** om het V2-eindpunt te gaan gebruiken. 
 

## <a name="expectations-of-performance-impact"></a>Verwachtingen over impact op prestaties  
Wanneer u het V2-eindpunt gebruikt, zijn prestatieverbeteringen een functie van het aantal gesynchroniseerde groepen, de grootte van die groepen en hun groepsverloop (de activiteit die het gevolg is van het toevoegen en verwijderen van gebruikers als leden van de groep). Het gebruik van het nieuwe eindpunt, zonder het aantal, de grootte of het verloop van de gesynchroniseerde groepen te verhogen, moet resulteren in kortere tijden voor exporteren en importeren naar Azure AD. 
 
De prestatieverbeteringen kunnen echter worden ontkracht door de aanvullende verwerking die is vereist bij het synchroniseren van grote groepen. U kunt uiteindelijk de totale synchronisatietijd verhogen door een te groot aantal groepen toe te voegen aan het synchronisatieproces.  

Als u meer inzicht wilt krijgen in de invloed van de toevoeging van de nieuwe groepen op uw synchronisatieprestaties, wordt u aangeraden om te beginnen met het synchroniseren van slechts enkele grote groepen met minder dan 100.000 leden. Vervolgens kunt u het aantal en de grootte van groepen verhogen door meer groepen binnen het bereik te brengen, via OE, kenmerk of maximale groepsgroottefiltering. De prestatieverbeteringen worden gerealiseerd voor de export- en importtaken voor de Azure AD-connector, niet voor de on-premises AD-connector. 

## <a name="deployment-step-by-step"></a>Implementatie stap voor stap 
De volgende drie fasen zijn een uitgebreid voorbeeld van het implementeren van het nieuwe V2-eindpunt.  Gebruik de fasen als richtlijn voor uw implementatie.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fase 1: de Azure AD Connect 
Het is raadzaam om eerst de stappen uit te voeren voor het installeren of upgraden naar Azure AD Connect versie [1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger en het synchronisatieproces te valideren voordat u naar de tweede fase gaat waarin u het V2-eindpunt inschakelen. Op de Azure AD Connect server: 


1. [Optioneel] Back-up van database maken 
2. Installeer of upgrade [naar Azure AD Connect versie 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) of hoger.
3. De installatie valideren 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fase 2: het V2-eindpunt inschakelen 
De volgende stap is het inschakelen van het V2-eindpunt. 

> [!NOTE]
> Nadat u het V2-eindpunt voor uw server hebt ingeschakeld, ziet u enkele prestatieverbeteringen voor uw bestaande workload. U kunt groepen echter nog niet synchroniseren met meer dan 50.000 leden. 

Als u wilt overschakelen naar het V2-eindpunt, gebruikt u de volgende stappen: 

1. Open een PowerShell-prompt als beheerder. 
2. Schakel de synchronisatieplanning uit nadat is gecontroleerd of er geen synchronisatiebewerkingen worden uitgevoerd: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importeer de nieuwe module: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Schakel over naar het v2-eindpunt:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
U hebt nu het V2-eindpunt voor uw server ingeschakeld. Neem de tijd om te controleren of er na het inschakelen van het V2-eindpunt geen onverwachte resultaten zijn voordat u verder gaat met de volgende fase waarin u de groepsgroottelimiet verhoogt. 
>[!NOTE]
>De bestands-/modulepaden kunnen een andere stationletter gebruiken, afhankelijk van het installatiepad dat is opgegeven bij het installeren van Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fase 3: de limiet voor groepslidmaatschap verhogen 
Nadat u hebt gecontroleerd of de service wordt uitgevoerd zonder onverwachte resultaten, kunt u doorgaan met het verhogen van de groepslidmaatschapslimiet. Het is raadzaam om eerst de lidmaatschapslimiet te verhogen naar een iets hogere waarde, bijvoorbeeld 75.000 leden om te zien hoe de grotere groepen worden gesynchroniseerd met Azure AD. Zodra u tevreden bent met de resultaten, kunt u de ledenlimiet verder verhogen.  

De maximumlimiet is 250.000 leden per groep. 

De volgende stappen kunnen worden gebruikt om de lidmaatschapslimiet te verhogen:  

1. Editor voor Azure AD-synchronisatieregels openen 
2. Kies in de editor **Uitgaand voor** Richting 
3. Klik op de **synchronisatieregel Out to AAD – Group Join** 
4. Klik op **de knop** Bewerken Schermopname met 'Uw synchronisatieregels weergeven en beheren' met ![ 'Out to AAD - Group Join' geselecteerd.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Klik op de **knop Ja** om de standaardregel uit te schakelen en een bewerkbare kopie te maken.
 ![Schermopname van het venster Bevestiging van gereserveerde regel bewerken met de knop Ja geselecteerd.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. Stel in het pop-upvenster op de pagina Beschrijving de prioriteit in op een beschikbare waarde tussen 1 en 99 Schermopname waarin het venster Regel voor uitgaande synchronisatie bewerken wordt weergegeven met Prioriteit  ![ gemarkeerd.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Werk op de pagina  **Transformaties** de  bronwaarde bij voor de lidtransformatie, en vervang '50000' door een waarde tussen 50001 en 250000. Deze vervanging verhoogt de maximale lidmaatschapsgrootte van groepen die worden gesynchroniseerd met Azure AD. We raden u aan te beginnen met een aantal van 100.000, om inzicht te krijgen in de impact die het synchroniseren van grote groepen heeft op uw synchronisatieprestaties. 
 
 **Voorbeeld** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Synchronisatieregel bewerken](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Klik op Opslaan 
10. PowerShell-prompt voor beheerder openen 
11. Synchronisatieplanning opnieuw inschakelen 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Als Azure AD Connect Health is ingeschakeld, wijzigt u de instellingen voor het Windows-toepassingsgebeurtenislogboek om de logboeken te archiveren in plaats van ze te overschrijven. De logboeken kunnen worden gebruikt om toekomstige problemen op te lossen. 

>[!NOTE]
> Nadat het nieuwe eindpunt is inschakelen, ziet u mogelijk extra exportfouten op de AAD-connector met de naam 'dn-attributes-failure'. Er is een bijbehorende gebeurtenislogboekinvoer voor elke fout met id 6949. De fouten zijn informatief en duiden niet op een probleem met uw installatie, maar in plaats van dat het synchronisatieproces bepaalde leden niet kan toevoegen aan een groep in Azure AD omdat het lidobject zelf niet is gesynchroniseerd met Azure AD. 

De nieuwe V2-eindpuntcode verwerkt sommige typen exportfouten die enigszins afwijken van de manier waarop de V1-code heeft gedaan.  Mogelijk ziet u meer informatie over foutberichten wanneer u het V2-eindpunt gebruikt. 

>[!NOTE]
> Wanneer u Azure AD Connect, moet u ervoor zorgen dat de stappen in fase 2 opnieuw worden uitgevoerd, omdat de wijzigingen niet behouden blijven tijdens het upgradeproces. 

Tijdens volgende verhogingen van de limiet voor groepslid in de synchronisatieregel Out **to AAD – Group Join** is geen volledige synchronisatie nodig, zodat u ervoor kunt kiezen om de volledige synchronisatie te onderdrukken door de volgende opdracht uit te voeren in PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Als u Microsoft 365 hebt die meer dan 50.000 leden hebben, worden de groepen in Azure AD Connect gelezen. Als terugschrijven van groepen is ingeschakeld, worden ze naar uw on-premises AD geschreven. 

## <a name="rollback"></a>Terugdraaiactie 
Als u het v2-eindpunt hebt ingeschakeld en moet worden terugdraaien, volgt u deze stappen: 

1. Op de Azure AD Connect server: a. [Optioneel] Back-up van database maken 
2. Open een PowerShell-prompt voor beheerders:
3. Schakel de synchronisatieplanning uit nadat is gecontroleerd of er geen synchronisatiebewerkingen worden uitgevoerd
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Overschakelen naar het V1-eindpunt * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Editor voor Azure AD-synchronisatieregels openen 
6. Verwijder de bewerkbare kopie van de **synchronisatieregel Out to AAD – Group Join** 
7. De standaardkopie van de synchronisatieregel **Out to AAD – Group Join** inschakelen 
8. Open een PowerShell-prompt voor beheerders 
9. Synchronisatieplanning opnieuw inschakelen 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Wanneer u terugschakelt van de V2-eindpunten naar V1-eindpunten, worden groepen die zijn gesynchroniseerd met meer dan 50.000 leden verwijderd nadat een volledige synchronisatie is uitgevoerd, voor zowel AD-groepen die zijn ingericht voor Azure AD als Microsoft 365 geïntegreerde groepen die zijn ingericht voor AD. 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen  
 
**Wanneer wordt het nieuwe eindpunt de standaard voor upgrades en nieuwe installaties?**  
</br>We zijn van plan om een nieuwe versie van AADConnect te publiceren voor download in februari 2021. In deze release wordt standaard gebruik gemaakt van het V2-eindpunt en kunnen groepen die groter zijn dan 50.000 zonder extra configuratie worden gesynchroniseerd. Deze release wordt vervolgens gepubliceerd voor automatische upgrade naar in aanmerking komende servers.
 
## <a name="next-steps"></a>Volgende stappen

* [Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
