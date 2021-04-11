---
title: 'Azure AD Connect synchronisatie met één object '
description: Meer informatie over het synchroniseren van een object van Active Directory naar Azure AD voor het oplossen van problemen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726048"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect synchronisatie met één object 

Het Azure AD Connect hulp programma voor enkelvoudige object synchronisatie is een Power shell-cmdlet die kan worden gebruikt om een afzonderlijk object te synchroniseren van Active Directory naar Azure Active Directory. Het gegenereerde rapport kan worden gebruikt om synchronisatie problemen per object te onderzoeken en op te lossen. 

> [!NOTE]
> Het hulp programma ondersteunt synchronisatie van Active Directory naar Azure Active Directory. Het biedt geen ondersteuning voor synchronisatie van Azure Active Directory naar Active Directory. 
>
> Het hulp programma ondersteunt het synchroniseren van een object wijziging toevoegen en bijwerken. Het synchronisatie van het verwijderen van een object wijziging wordt niet ondersteund. 

## <a name="how-it-works"></a>Uitleg
Het hulp programma voor het synchroniseren van één object vereist een Active Directory DN-naam als invoer om de bron connector en de partitie voor het importeren te vinden. De wijzigingen worden geëxporteerd naar Azure Active Directory. Het hulp programma genereert een JSON-uitvoer vergelijkbaar met het resource type **provisioningObjectSummary** . 

Het hulp programma voor het synchroniseren van één object voert de volgende stappen uit: 

 1. Bepalen of het object (bron) domein (Active Directory-connector en-partitie) in het synchronisatie bereik is. 
 2. Bepaal of het object (doel) domein (Azure Active Directory connector en partitie) in het synchronisatie bereik. 
 3. Bepalen of de organisatie-eenheid van het object in het synchronisatie bereik is. 
 4. Bepalen of een object toegankelijk is met de referenties van het Connector-account. 
 5. Bepalen of het object type in het synchronisatie bereik is. 
 6. Bepalen of het object zich in een synchronisatie bereik bevindt als groeps filtering is ingeschakeld. 
 7. Importeer het object van Active Directory naar Active Directory-Connector ruimte. 
 8. Importeer het object van Azure Active Directory naar Azure Active Directory-Connector ruimte. 
 9. Synchroniseer het object vanuit Active Directory-Connector ruimte. 
 10. Exporteer het object uit Azure Active Directory-Connector ruimte naar Azure Active Directory. 

Naast de JSON-uitvoer genereert het hulp programma een HTML-rapport met alle details van de synchronisatie bewerking. Het HTML-rapport bevindt zich in **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> . htm**. Dit HTML-rapport kan worden gedeeld met het ondersteunings team, indien nodig om verdere problemen op te lossen. 

Het HTML-rapport heeft de volgende opties: 

|Tabblad|Beschrijving|
|-----|-----|
|Stappen|Geeft een overzicht van de stappen die zijn uitgevoerd om een object te synchroniseren. Elke stap bevat Details voor het oplossen van problemen. De stappen voor importeren, synchroniseren en exporteren bevatten extra kenmerk gegevens, zoals naam, op meerdere waarden, type, waarde, waarde toevoegen, waarde verwijderen, bewerking, synchronisatie regel, toewijzings type en gegevens bron.| 
|& aanbeveling voor problemen oplossen|Bevat de fout code en de reden. De informatie over de fout is alleen beschikbaar als er een fout optreedt.| 
|Gewijzigde eigenschappen|De oude waarde en de nieuwe waarde worden weer gegeven. Als er geen oude waarde is of als de nieuwe waarde wordt verwijderd, is die cel leeg. Voor kenmerken met meerdere waarden wordt het aantal weer gegeven. De naam van het kenmerk is een koppeling naar het tabblad stappen: object exporteren vanuit Azure Active Directory-Connector ruimte naar Azure Active Directory: kenmerk info met aanvullende details van het kenmerk zoals naam, is meerdere waarden, type, waarde, waarde toevoegen, waarde verwijderen, bewerking, synchronisatie regel, toewijzings type en gegevens bron.| 
|Samenvatting|Hierin wordt een overzicht gegeven van wat er is gebeurd en id's voor het object in de bron-en doel systemen.| 

## <a name="prerequisites"></a>Vereisten 

Als u het hulp programma voor het synchroniseren van één object wilt gebruiken, moet u de versie van 2021 maart van Azure AD Connect of hoger gebruiken. 

### <a name="run-the-single-object-sync-tool"></a>Het hulp programma voor het synchroniseren van één object uitvoeren 

Voer de volgende stappen uit om het hulp programma voor het synchroniseren van één object uit te voeren: 

 1. Open een nieuwe Windows Power shell-sessie op uw Azure AD Connect-server met de optie als administrator uitvoeren. 

 2. Stel het [uitvoerings beleid](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) in op RemoteSigned of onbeperkt. 

 3. Schakel de synchronisatie planner uit nadat u hebt gecontroleerd of er geen synchronisatie bewerkingen worden uitgevoerd. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. De AdSync-module voor diagnostische gegevens importeren 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Roep de cmdlet voor het synchroniseren van enkelvoudige objecten aan. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Schakel de synchronisatie planner opnieuw in. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Invoer parameters voor synchronisatie enkelvoudige object|Description| 
|-----|----|
|DistinguishedName|Dit is een vereiste teken reeks parameter. </br></br>Dit is de DN-naam van het Active Directory-object waarvoor synchronisatie en probleem oplossing nodig zijn.| 
|StagingMode|Dit is een optionele switch parameter.</br></br>Deze para meter kan worden gebruikt om te voor komen dat de wijzigingen naar Azure Active Directory worden geëxporteerd.</br></br>**Opmerking**: de cmdlet voert de synchronisatie bewerking door. </br></br>**Opmerking**: Azure AD Connect staging server exporteert de wijzigingen niet naar Azure Active Directory.|
|NoHtmlReport|Dit is een optionele switch parameter.</br></br>Deze para meter kan worden gebruikt om te voor komen dat het HTML-rapport wordt gegenereerd. 

## <a name="single-object-sync-throttling"></a>Beperking van het synchroniseren van enkelvoudige objecten 

Het hulp programma voor het synchroniseren van één object **is** bedoeld voor het onderzoeken en oplossen van problemen per object synchronisatie. Het is **niet** bedoeld om de synchronisatie cyclus die door de scheduler wordt uitgevoerd, te vervangen. Voor het importeren van Azure Active Directory en het exporteren naar Azure Active Directory gelden beperkingen voor het beperken van limieten. Probeer het na 5 minuten opnieuw als u de beperkings limiet bereikt. 

## <a name="next-steps"></a>Volgende stappen
- [Problemen met object synchronisatie oplossen](tshoot-connect-objectsync.md)
- [Problemen met object oplossen niet synchroniseren](tshoot-connect-object-not-syncing.md)
- [End-to-end-probleem oplossing van Azure AD Connect objecten en-kenmerken](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
