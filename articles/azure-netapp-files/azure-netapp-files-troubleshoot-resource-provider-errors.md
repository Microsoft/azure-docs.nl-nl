---
title: Fouten bij het oplossen van problemen met Azure NetApp Files Resource providers | Microsoft Docs
description: Beschrijft oorzaken, oplossingen en tijdelijke oplossingen voor veelvoorkomende fouten in Azure NetApp Files van resource providers.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: b-juche
ms.openlocfilehash: ac83e5a9366a12c5adce0e08f04f2bb28a7d788d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374878"
---
# <a name="troubleshoot-azure-netapp-files-resource-provider-errors"></a>Problemen met Azure NetApp Files-resourceproviders oplossen 

In dit artikel worden veelvoorkomende fouten van Azure NetApp Files Resource providers, de oorzaken, oplossingen en tijdelijke oplossingen beschreven (indien beschikbaar).

## <a name="common-azure-netapp-files-resource-provider-errors"></a>Veelvoorkomende fouten in Azure NetApp Files Resource provider

***Het maken van `netAppAccounts` is beperkt in deze regio.***

Deze situatie treedt op wanneer het abonnement waitlisted is voor Azure NetApp Files en de gebruiker probeert een NetApp-account te maken.

* Oorzaak:   
De Azure-resource provider voor Azure NetApp Files is niet geregistreerd. 
 
* Oplossing:   
Voer alle stappen uit die worden beschreven in [Azure NetApp resource provider registratie](azure-netapp-files-register.md#resource-provider) nadat uw abonnement waitlisted is.

***BareMetalTenantId kan niet worden gewijzigd.***  

Deze fout treedt op wanneer u een volume probeert bij te werken of te patchen en de `BaremetalTenantId` eigenschap een gewijzigde waarde heeft.

* Oorzaak:   
U probeert een volume bij te werken en de `BaremetalTenantId` eigenschap heeft een andere waarde dan de waarde die is opgeslagen in Azure.
* Oplossing:   
Neem niets `BaremetalTenantId` op in de patch-en update-aanvraag (put). U kunt er ook voor zorgen dat `BaremetalTenantId` hetzelfde is in de aanvraag.

***ServiceLevel kan niet worden gewijzigd.***  

Deze fout treedt op wanneer u een capaciteits groep probeert bij te werken of te patchen met een ander service niveau wanneer de capaciteits pool al volumes bevat.

* Oorzaak:   
U probeert een service niveau voor de capaciteits groep bij te werken wanneer de groep volumes bevat.
* Oplossing:   
Verwijder alle volumes uit de capaciteits groep en wijzig vervolgens het service niveau.
* Tijdelijke oplossing:   
Maak een andere capaciteits groep en maak de volumes opnieuw in de nieuwe capaciteits groep.

***PoolId kan niet worden gewijzigd***  

Deze fout treedt op wanneer u een capaciteits groep wilt bijwerken of een patch wilt uitvoeren met een gewijzigde `PoolId` eigenschap.

* Oorzaak:   
U probeert een eigenschap van een capaciteits groep bij te werken `PoolId` . De `PoolId` eigenschap is een alleen-lezen eigenschap en kan niet worden gewijzigd.
* Oplossing:   
Neem niets `PoolId` op in de patch-en update-aanvraag (put).  U kunt er ook voor zorgen dat `PoolId` hetzelfde is in de aanvraag.

***CreationToken kan niet worden gewijzigd.***

Deze fout treedt op wanneer u probeert het bestandspad () te wijzigen `CreationToken` nadat het volume is gemaakt. Het bestandspad ( `CreationToken` ) moet worden ingesteld wanneer het volume wordt gemaakt en kan later niet meer worden gewijzigd.

* Oorzaak:   
U probeert het bestandspad ( `CreationToken` ) te wijzigen nadat het volume is gemaakt. Dit is een niet-ondersteunde bewerking. 
* Oplossing:   
Als het wijzigen van het bestandspad niet nodig is, kunt u de para meter verwijderen uit de aanvraag om het fout bericht te sluiten.
* Tijdelijke oplossing:   
Als u het bestandspad () moet wijzigen `CreationToken` , kunt u een nieuw volume maken met een nieuw bestandspad en vervolgens de gegevens migreren naar het nieuwe volume.

***CreationToken moet ten minste 16 tekens lang zijn.***

Deze fout treedt op wanneer het bestandspad ( `CreationToken` ) niet voldoet aan de lengte vereiste. De lengte van het bestandspad moet ten minste één teken lang zijn.

* Oorzaak:   
Het bestandspad is leeg.  Wanneer u een volume maakt met behulp van de API, is een aanmaak token vereist. Als u de Azure Portal gebruikt, wordt het bestandspad automatisch gegenereerd.
* Oplossing:   
Voer ten minste één teken in als het bestandspad ( `CreationToken` ).

***De domein naam kan niet worden gewijzigd.***

Deze fout treedt op wanneer u de domein naam in Active Directory probeert te wijzigen.

* Oorzaak:   
U probeert de eigenschap domain name bij te werken.
* Oplossing:    
Geen. U kunt de domein naam niet wijzigen.
* Tijdelijke oplossing:   
Verwijder alle volumes met behulp van de configuratie van Active Directory. Verwijder vervolgens de Active Directory configuratie en maak de volumes opnieuw.

***Fout met dubbele waarde voor object ExportPolicy. rules [RuleIndex].***

Deze fout treedt op wanneer het export beleid niet is gedefinieerd met een unieke index. Wanneer u een export beleid definieert, moeten alle export beleids regels een unieke index tussen 1 en 5 hebben.

* Oorzaak:   
Het gedefinieerde export beleid voldoet niet aan de vereisten voor het exporteren van beleids regels. U moet één beleids regel voor exporteren hebben met de minimale en vijf export beleids regels met het maximum.
* Oplossing:   
Zorg ervoor dat de index nog niet wordt gebruikt en dat deze zich in het bereik van 1 tot 5 bevindt.
* Tijdelijke oplossing:   
Gebruik een andere index voor de regel die u wilt instellen.

***Fout {Action} {resourceTypeName}***

Deze fout wordt weer gegeven wanneer een andere fout afhandeling de fout niet heeft verwerkt tijdens het uitvoeren van een actie op een resource.   De tekst ' fout ' bevat. Dit `{action}` kan een van de zijn ( `getting` ,, `creating` `updating` , of `deleting` ).  De is de (bijvoorbeeld,,, enzovoort `{resourceTypeName}` `resourceTypeName` `netAppAccount` `capacityPool` `volume` ).

* Oorzaak:   
Deze fout is een onverwerkte uitzonde ring waarbij de oorzaak niet bekend is.
* Oplossing:   
Neem contact op met het ondersteunings centrum van Azure om de gedetailleerde reden in Logboeken te melden.
* Tijdelijke oplossing:   
Geen.

***De naam van het bestandspad mag alleen letters, cijfers en afbreek streepjes (""-"") bevatten.***

Deze fout treedt op wanneer het bestandspad niet-ondersteunde tekens bevat, bijvoorbeeld een punt ("."), een komma (","), een onderstrepings teken ("_") of een dollar teken ("$").

* Oorzaak:   
Het bestandspad bevat niet-ondersteunde tekens, bijvoorbeeld een punt ("."), komma (","), onderstrepings teken ("_") of het dollar teken ("$").
* Oplossing:   
Verwijder tekens die geen alfabetische letters, cijfers of afbreek streepjes ('-') bevatten uit het bestandspad dat u hebt ingevoerd.
* Tijdelijke oplossing:   
U kunt een onderstrepings teken vervangen door een koppel teken of een hoofd letter gebruiken in plaats van spaties om het begin van nieuwe woorden aan te geven.  Gebruik bijvoorbeeld ' NewVolume ' in plaats van ' New volume '.

***FileSystemId kan niet worden gewijzigd.***

Deze fout treedt op wanneer u probeert te wijzigen `FileSystemId` .  Wijzigen `FileSystemdId` is geen ondersteunde bewerking. 

* Oorzaak:   
De ID van het bestands systeem wordt ingesteld wanneer het volume wordt gemaakt. `FileSystemId` kan later niet worden gewijzigd.
* Oplossing:   
Neem niets `FileSystemId` op in een patch-en update-aanvraag (put).  U kunt er ook voor zorgen dat `FileSystemId` hetzelfde is in de aanvraag.

***ActiveDirectory met id: {string} bestaat niet.***

Het `{string}` gedeelte is de waarde die u hebt ingevoerd in de `ActiveDirectoryId` eigenschap voor de Active Directory verbinding.

* Oorzaak:   
Wanneer u een account met de configuratie van Active Directory hebt gemaakt, hebt u een waarde ingevoerd `ActiveDirectoryId` die moet leeg zijn.
* Oplossing:   
Neem niet `ActiveDirectoryId` op in de aanvraag maken (put).

***Ongeldige API-versie.***

De API-versie is niet verzonden of bevat een ongeldige waarde.

* Oorzaak:   
De waarde in de query parameter `api-version` bevat een ongeldige waarde.
* Oplossing:   
Gebruik de juiste API-versie waarde.  De resource provider ondersteunt veel API-versies. De waarde heeft de notatie JJJJ-MM-DD.

***Er is een ongeldige waarde {Value} ontvangen voor {1} .***

Dit bericht geeft aan dat er een fout is opgetreden in de velden voor,,,, `RuleIndex` `AllowedClients` `UnixReadOnly` `UnixReadWrite` `Nfsv3` en `Nfsv4` .

* Oorzaak:   
De invoer validatie aanvraag is mislukt voor ten minste een van de volgende velden: `RuleIndex` , `AllowedClients` , `UnixReadOnly` , `UnixReadWrite` , `Nfsv` 3 en `Nfsv4` .
* Oplossing:   
Zorg ervoor dat u alle vereiste en niet-conflicterende para meters op de opdracht regel instelt. U kunt bijvoorbeeld niet zowel de als- `UnixReadOnly` `UnixReadWrite` para meters op hetzelfde moment instellen.
* Tijdelijke oplossing:   
Zie de bovenstaande oplossing.

***IP-bereik voor {0} {1} VLAN {2} wordt al gebruikt***

Deze fout treedt op omdat de interne records van het gebruikte IP-bereik een conflict veroorzaken met het zojuist toegewezen IP-adres.

* Oorzaak:   
Het IP-adres dat is toegewezen voor het maken van het volume is al geregistreerd.
De reden kan een eerder mislukt volume zijn.
* Oplossing:   
Neem contact op met Azure-ondersteunings centrum.

***Ontbrekende waarde voor {Property}.***

Deze fout geeft aan dat een vereiste eigenschap ontbreekt in de aanvraag. De teken reeks {Property} bevat de naam van de ontbrekende eigenschap.

* Oorzaak:   
De invoer validatie aanvraag is voor ten minste een van de eigenschappen mislukt.
* Oplossing:   
Zorg ervoor dat u alle vereiste en niet-conflicterende eigenschappen instelt in de aanvraag, speciaal, de eigenschap van het fout bericht.

***MountTargets kan niet worden gewijzigd.***

Deze fout treedt op wanneer een gebruiker de eigenschap volume MountTargets probeert bij te werken of te patchen.

* Oorzaak:   
U probeert de volume-eigenschap bij te werken `MountTargets` . Het wijzigen van deze eigenschap wordt niet ondersteund.
* Oplossing:   
Neem niets `MountTargets` op in een patch-en update-aanvraag (put).  U kunt er ook voor zorgen dat `MountTargets` hetzelfde is in de aanvraag.

***De naam wordt al gebruikt.***

Deze fout geeft aan dat de naam van de resource al in gebruik is.

* Oorzaak:   
U probeert een resource te maken met een naam die wordt gebruikt voor een bestaande resource.
* Oplossing:   
Gebruik een unieke naam bij het maken van de resource.

***Het bestandspad wordt al gebruikt.***

Deze fout geeft aan dat het bestandspad voor het volume al in gebruik is.

* Oorzaak:   
U probeert een volume te maken met een bestandspad dat gelijk is aan het bestaande volume.
* Oplossing:   
Gebruik een uniek bestandspad bij het maken van het volume.

***De naam is te lang.***

Deze fout geeft aan dat de resource naam niet voldoet aan de vereiste voor de maximum lengte.

* Oorzaak:   
De resource naam is te lang.
* Oplossing:   
Gebruik een kortere naam voor de resource.

***Het bestandspad is te lang.***

Deze fout geeft aan dat het bestandspad voor het volume niet voldoet aan de vereiste voor de maximum lengte.

* Oorzaak:   
Het pad naar het volume bestand is te lang.
* Oplossing:   
Gebruik een kortere bestands locatie.

***De naam is te kort.***

Deze fout geeft aan dat de resource naam niet voldoet aan de vereiste voor de minimum lengte.

* Oorzaak:   
De resource naam is te kort.
* Oplossing:   
Gebruik een langere naam voor de resource.

***Het bestandspad is te kort.***

Deze fout geeft aan dat het pad naar het volume bestand niet voldoet aan de vereiste voor de minimum lengte.

* Oorzaak:   
Het pad naar het volume bestand is te kort.
* Oplossing:   
Verg root de lengte van het pad naar het volume bestand.

***De Azure NetApp Files-API is onbereikbaar.***

De Azure-API is afhankelijk van de Azure NetApp Files-API voor het beheren van volumes. Deze fout wijst op een probleem met de API-verbinding.

* Oorzaak:   
De onderliggende API reageert niet, wat resulteert in een interne fout. Deze fout is waarschijnlijk tijdelijk.
* Oplossing:   
Het probleem is waarschijnlijk tijdelijk. De aanvraag moet na enige tijd slagen.
* Tijdelijke oplossing:   
Geen. De onderliggende API is essentieel voor het beheren van volumes.

***Er is geen resultaat-id van de bewerking gevonden voor {0} .***

Deze fout geeft aan dat de bewerking niet kan worden voltooid vanwege een interne fout.

* Oorzaak:   
Er is een interne fout opgetreden waardoor de bewerking niet kan worden voltooid.
* Oplossing:   
Deze fout is waarschijnlijk tijdelijk. Wacht een paar minuten en probeer het opnieuw. Als het probleem zich blijft voordoen, maakt u een ticket voor technische ondersteuning om het probleem te onderzoeken.
* Tijdelijke oplossing:   
Wacht een paar minuten en controleer of het probleem zich blijft voordoen.

***Het is niet toegestaan om protocol typen CIFS en NFS te combi neren***

Deze fout treedt op wanneer u een volume probeert te maken en er zijn beide typen CIFS (SMB) en NFS-protocol in de volume-eigenschappen.

* Oorzaak:   
De protocol typen CIFS (SMB) en NFS worden gebruikt in de volume-eigenschappen.
* Oplossing:   
Verwijder een van de protocol typen.
* Tijdelijke oplossing:   
Laat de eigenschap protocol type leeg of null.

***Aantal items: {value} voor object: ExportPolicy. rules [RuleIndex] ligt buiten het minimum bereik.***

Deze fout treedt op wanneer de export beleids regels niet voldoen aan het minimum-of maximum bereik vereiste. Als u het export beleid definieert, moet er één beleids regel voor het exporteren van de minimum-en vijf export beleids regels zijn ingesteld op het maximum.

* Oorzaak:   
Het export beleid dat u hebt gedefinieerd, voldoet niet aan het vereiste bereik.
* Oplossing:   
Zorg ervoor dat de index niet al wordt gebruikt en dat deze zich in het bereik van 1 tot 5 bevindt.
* Tijdelijke oplossing:   
Het is niet verplicht om export beleid te gebruiken op de volumes. U kunt het export beleid volledig weglaten als u geen export beleids regels hoeft te gebruiken.

***Er is slechts één Active Directory toegestaan***

Deze fout treedt op wanneer u een Active Directory configuratie probeert te maken en er al een bestaat voor het abonnement in de regio. De fout kan ook optreden wanneer u probeert meer dan een Active Directory configuratie te maken.

* Oorzaak:   
U probeert een Active Directory te maken (niet bij te werken), maar er bestaat al een.
* Oplossing:   
Als de Active Directory configuratie niet wordt gebruikt, kunt u eerst de bestaande configuratie verwijderen en de bewerking opnieuw uitvoeren.
* Tijdelijke oplossing:   
Geen. Er is slechts één Active Directory toegestaan.

***De bewerking {Operation} wordt niet ondersteund.***

Deze fout geeft aan dat de bewerking niet beschikbaar is voor het actieve abonnement of de resource.

* Oorzaak:   
De bewerking is niet beschikbaar voor het abonnement of de resource.
* Oplossing:   
Zorg ervoor dat de bewerking correct is ingevoerd en dat deze beschikbaar is voor de resource en het abonnement die u gebruikt.

***OwnerId kan niet worden gewijzigd***

Deze fout treedt op wanneer u de eigenschap OwnerId van het volume probeert te wijzigen. Het wijzigen van de OwnerId is geen ondersteunde bewerking. 

* Oorzaak:   
De `OwnerId` eigenschap wordt ingesteld wanneer het volume wordt gemaakt. De eigenschap kan later niet worden gewijzigd.
* Oplossing:   
Neem niets `OwnerId` op in een patch-en update-aanvraag (put). U kunt er ook voor zorgen dat `OwnerId` hetzelfde is in de aanvraag.

***Bovenliggende groep niet gevonden***

Deze fout treedt op wanneer u probeert een volume te maken en de capaciteits pool waarin u het volume maakt, niet is gevonden.

* Oorzaak:   
De capaciteits groep waar het volume wordt gemaakt, is niet gevonden.
* Oplossing:   
Waarschijnlijk is de groep niet volledig gemaakt of is deze al verwijderd op het moment dat het volume werd gemaakt.

***De patch bewerking wordt niet ondersteund voor dit bron type.***

Deze fout treedt op wanneer u probeert het koppel doel of de moment opname te wijzigen.

* Oorzaak:   
Het koppel doel wordt gedefinieerd wanneer het wordt gemaakt en kan daarna niet meer worden gewijzigd.
De moment opnamen bevatten geen eigenschappen die kunnen worden gewijzigd.
* Oplossing:   
Geen. Deze resources hebben geen eigenschappen die kunnen worden gewijzigd.

***De groeps grootte is te klein voor de totale volume grootte.***

Deze fout treedt op wanneer u de grootte van de capaciteits pool bijwerkt en de grootte kleiner is dan de totale `usedBytes` waarde van alle volumes in die capaciteits groep.  Deze fout kan ook optreden wanneer u een nieuw volume maakt of het formaat van een bestaand volume wijzigt en de nieuwe grootte van het volume groter is dan de beschik bare ruimte in de capaciteits groep.

* Oorzaak:   
U probeert de capaciteits groep bij te werken naar een kleinere grootte dan usedBytes in alle volumes van de capaciteits groep.  Of u probeert een volume te maken dat groter is dan de beschik bare ruimte in de capaciteits groep.  U kunt ook de grootte van een volume wijzigen en de nieuwe grootte overschrijdt de beschik bare ruimte in de capaciteits groep.
* Oplossing:   
Stel de grootte van de capaciteits pool in op een grotere waarde of maak een kleiner volume voor een volume.
* Tijdelijke oplossing:   
Verwijder voldoende volumes zodat de grootte van de capaciteits groep kan worden bijgewerkt naar deze grootte.

***De eigenschap: de locatie voor de moment opname moet hetzelfde zijn als het volume***

Deze fout treedt op wanneer u een moment opname maakt met een andere locatie dan het volume dat eigenaar is van de moment opname.

* Oorzaak:   
Ongeldige waarde in de locatie-eigenschap voor de moment opname.
* Oplossing:   
Stel een geldige teken reeks in voor de eigenschap location.

***De naam van {resource type} moet gelijk zijn aan de naam van de resource-id.***

Deze fout treedt op wanneer u een resource maakt en u de eigenschap name invult met een andere waarde dan de eigenschap name van `resourceId` .

* Oorzaak:   
Ongeldige waarde in de eigenschap name bij het maken van een resource.
* Oplossing:   
Laat de eigenschap name leeg of laat het toe om dezelfde waarde te gebruiken als de eigenschap naam (tussen de laatste back slash "/" en het vraag teken "?") in `resourceId` .

***Het protocol type {Value} is niet bekend***

Deze fout treedt op wanneer u een volume met een onbekend protocol type maakt.  Geldige waarden zijn "NFSv3", "NFSv4" en "CIFS".

* Oorzaak:   
U probeert een ongeldige waarde in te stellen in de `protocolType` eigenschap volume.
* Oplossing:   
Stel een geldige teken reeks in `protocolType` .
* Tijdelijke oplossing:   
Stel `protocolType` in op null.

***Protocol typen kunnen niet worden gewijzigd***

Deze fout treedt op wanneer u een volume probeert bij te werken of te patchen `ProtocolType` .  Het wijzigen van protocol type is geen ondersteunde bewerking.

* Oorzaak:   
De `ProtocolType` eigenschap wordt ingesteld wanneer het volume wordt gemaakt.  Het kan niet worden bijgewerkt.
* Oplossing:   
Geen.
* Tijdelijke oplossing:   
Maak een ander volume met nieuwe protocol typen.

***Het maken van de resource van het type {resource soort} overschrijdt het quotum van {quota} resources van het type {resource soort} per {parentResourceType}. Het huidige aantal resources is {currentCount}. Verwijder een aantal bronnen van dit type voordat u een nieuw item maakt.***

Deze fout treedt op wanneer u een bron (,, of) probeert te maken `NetAppAccount` `CapacityPool` `Volume` `Snapshot` , maar het quotum de limiet heeft bereikt.

* Oorzaak:   
U probeert een resource te maken, maar de quotum limiet is bereikt (voor beeld: `NetAppAccounts` per abonnement of `CapacityPools` per `NetAppAccount` ).
* Oplossing:   
Verhoog de quotum limiet.
* Tijdelijke oplossing:   
Verwijder ongebruikte resources van hetzelfde type en maak deze opnieuw.

***Er is een waarde ontvangen voor de eigenschap {PropertyName} met het kenmerk alleen-lezen.***

Deze fout treedt op wanneer u een waarde definieert voor een eigenschap die niet kan worden gewijzigd. U kunt de volume-ID bijvoorbeeld niet wijzigen.

* Oorzaak:   
U probeert een para meter (bijvoorbeeld de volume-ID) te wijzigen die niet kan worden gewijzigd.
* Oplossing:   
Wijzig geen waarde voor de eigenschap.

***De aangevraagde {resource} is niet gevonden.***

Deze fout treedt op wanneer u probeert te verwijzen naar een niet-bestaande resource, bijvoorbeeld een volume of een moment opname. De resource is mogelijk verwijderd of heeft een speld resource naam.

* Oorzaak:   
U probeert te verwijzen naar een niet-bestaande resource (bijvoorbeeld een volume of moment opname) die al is verwijderd of een verkeerd gespelde resource naam heeft.
* Oplossing:   
Controleer de vraag op spel fouten om er zeker van te zijn dat deze correct wordt verwezen.
* Tijdelijke oplossing:   
Zie de sectie oplossing hierboven.

***Het service niveau {volumeServiceLevel} is hoger dan het bovenliggende {poolServiceLevel}***

Deze fout treedt op wanneer u een volume maakt of bijwerkt en u het service niveau hebt ingesteld op een hoger niveau dan de capaciteits groep die deze bevat.

* Oorzaak:   
U probeert een volume te maken of bij te werken met een hoger geclassificeerd service niveau dan de bovenliggende capaciteits groep.
* Oplossing:   
Stel het service niveau in op dezelfde of een lagere positie dan de bovenliggende capaciteits groep.
* Tijdelijke oplossing:   
Maak het volume in een andere capaciteits pool met een correct service niveau. U kunt ook alle volumes uit de capaciteits groep verwijderen en service niveau voor de capaciteits groep instellen op een hogere positie.

***De naam van de SMB-server mag niet langer zijn dan 10 tekens.***

Deze fout treedt op wanneer u een Active Directory configuratie voor een account maakt of bijwerkt.

* Oorzaak:   
De lengte van de SMB-server naam is langer dan 10 tekens.
* Oplossing:   
Gebruik een kortere server naam. De maximale lengte is 10 tekens.
* Tijdelijke oplossing:   
Geen.  Zie de bovenstaande oplossing. 

***SubnetId kan niet worden gewijzigd.***

Deze fout treedt op wanneer u probeert het volume te wijzigen `subnetId` nadat het is gemaakt.  `SubnetId` moet worden ingesteld wanneer het volume wordt gemaakt en kan later niet meer worden gewijzigd.

* Oorzaak:   
U probeert het te wijzigen `subnetId` nadat het volume is gemaakt. Dit is een niet-ondersteunde bewerking. 
* Oplossing:   
Als het `subnetId` niet nodig is om het te wijzigen, kunt u de para meter verwijderen uit de aanvraag om het fout bericht te sluiten.
* Tijdelijke oplossing:   
Als u het moet wijzigen `subnetId` , kunt u een nieuw volume maken met een nieuwe `subnetId` en vervolgens de gegevens naar het nieuwe volume migreren.

***SubnetId heeft een ongeldige indeling.***

Deze fout treedt op wanneer u een nieuw volume probeert te maken, maar het `subnetId` is geen `resourceId` voor een subnet.

* Oorzaak:   
Deze fout treedt op wanneer u een nieuw volume probeert te maken, maar het `subnetId` is geen `resourceId` voor een subnet. 
* Oplossing:   
Controleer de waarde voor de om te controleren of `subnetId` deze een bevat `resourceId` voor het gebruikte subnet.
* Tijdelijke oplossing:   
Geen. Zie de bovenstaande oplossing. 

***Het subnet moet de overdracht ' micro soft. NetApp/volumes ' hebben.***

Deze fout treedt op wanneer u een volume maakt en het geselecteerde subnet niet wordt overgedragen `Microsoft.NetApp/volumes` .

* Oorzaak:   
U hebt geprobeerd het volume te maken en u hebt een subnet geselecteerd dat niet is overgedragen `Microsoft.NetApp/volumes` .
* Oplossing:   
Selecteer een ander subnet dat wordt overgedragen `Microsoft.NetApp/volumes` .
* Tijdelijke oplossing:   
Voeg een juiste overdracht toe aan het subnet.

***Het opgegeven resource type is onbekend/niet van toepassing.***

Deze fout treedt op wanneer een naam controle is aangevraagd voor een niet-toepasselijk resource type of voor een onbekend resource type.

* Oorzaak:   
Er is een naam controle aangevraagd voor een onbekend of niet-ondersteund resource type.
* Oplossing:   
Controleer of de resource waarvoor u de aanvraag uitvoert, wordt ondersteund of geen spel fouten bevat.
* Tijdelijke oplossing:   
Zie de bovenstaande oplossing.

***Onbekende Azure NetApp Files fout.***

De Azure-API is afhankelijk van de Azure NetApp Files-API voor het beheren van volumes. De fout geeft aan dat er een probleem is in de communicatie met de API.

* Oorzaak:   
De onderliggende API verzendt een onbekende fout. Deze fout is waarschijnlijk tijdelijk.
* Oplossing:   
Het probleem is waarschijnlijk tijdelijk en de aanvraag moet na enige tijd slagen. Als het probleem zich blijft voordoen, maakt u een ondersteunings ticket om het probleem te onderzoeken.
* Tijdelijke oplossing:   
Geen. De onderliggende API is essentieel voor het beheren van volumes.

***Ontvangen waarde voor een onbekende eigenschap {PropertyName}.***

Deze fout treedt op wanneer er niet-bestaande eigenschappen worden gegeven voor een resource zoals het volume, de moment opname of het doel van de koppeling.

* Oorzaak:   
De aanvraag heeft een reeks eigenschappen die kunnen worden gebruikt voor elke resource. U kunt geen niet-bestaande eigenschappen in de aanvraag toevoegen.
* Oplossing:   
Zorg ervoor dat alle eigenschapnamen juist zijn gespeld en dat de eigenschappen beschikbaar zijn voor het abonnement en de resource.
* Tijdelijke oplossing:   
Verminder het aantal eigenschappen dat is gedefinieerd in de aanvraag om de eigenschap te elimineren die de fout veroorzaakt.

***Update-bewerking wordt niet ondersteund voor dit bron type.***

Alleen volumes kunnen worden bijgewerkt. Deze fout treedt op wanneer u een niet-ondersteunde update bewerking probeert uit te voeren, bijvoorbeeld het bijwerken van een moment opname.

* Oorzaak:   
De resource die u wilt bijwerken, biedt geen ondersteuning voor de update bewerking. De eigenschappen van alleen volumes kunnen worden gewijzigd.
* Oplossing:   
Geen. De resource die u wilt bijwerken, biedt geen ondersteuning voor de update bewerking. Daarom kan deze niet worden gewijzigd.
* Tijdelijke oplossing:   
Voor een volume maakt u een nieuwe resource met de update op de locatie en migreert u de gegevens.

***Volume kan niet worden gemaakt in een groep die niet de status geslaagd heeft.***

Deze fout treedt op wanneer u een volume in een groep probeert te maken dat niet de status voltooid heeft. De bewerking voor het maken van de capaciteits groep is waarschijnlijk om een of andere reden mislukt.

* Oorzaak:   
De capaciteits groep met het nieuwe volume heeft de status mislukt.
* Oplossing:   
Controleer of de capaciteits groep is gemaakt en niet de status Mislukt heeft.
* Tijdelijke oplossing:   
Maak een nieuwe capaciteits groep en maak het volume in de nieuwe groep.

***Het volume wordt gemaakt en kan op dit moment niet worden verwijderd.***

Deze fout treedt op wanneer u een volume probeert te verwijderen dat nog wordt gemaakt.

* Oorzaak:   
Er wordt nog een volume gemaakt wanneer u het volume probeert te verwijderen.
* Oplossing:   
Wacht tot het maken van het volume is voltooid en probeer het opnieuw.
* Tijdelijke oplossing:   
Zie de bovenstaande oplossing.

***Het volume wordt verwijderd en kan op dit moment niet worden verwijderd.***

Deze fout treedt op wanneer u een volume probeert te verwijderen wanneer dit al wordt verwijderd.

* Oorzaak:   
Er wordt al een volume verwijderd wanneer u het volume probeert te verwijderen.
* Oplossing:   
Wacht tot de huidige verwijderings bewerking is voltooid.
* Tijdelijke oplossing:   
Zie de bovenstaande oplossing.

***Het volume wordt bijgewerkt en kan op dit moment niet worden verwijderd.***

Deze fout treedt op wanneer u een volume probeert te verwijderen dat wordt bijgewerkt.

* Oorzaak:   
Er wordt een volume bijgewerkt wanneer u het volume probeert te verwijderen.
* Oplossing:   
Wacht tot de update bewerking is voltooid en probeer het opnieuw.
* Tijdelijke oplossing:   
Zie de bovenstaande oplossing.

***Het volume is niet gevonden of is niet gemaakt.***

Deze fout treedt op wanneer het maken van het volume is mislukt en u probeert het volume te wijzigen of een moment opname te maken voor het volume.

* Oorzaak:   
Het volume bestaat niet of het maken is mislukt.
* Oplossing:   
Controleer of u het juiste volume wijzigt en het maken van het volume is geslaagd. U kunt ook controleren of het volume waarvoor u een moment opname maakt bestaat.
* Tijdelijke oplossing:   
Geen.  Zie de bovenstaande oplossing. 

***Het opgegeven token voor maken bestaat al***

Deze fout treedt op wanneer u een volume probeert te maken en u een aanmaak token (exportpad) opgeeft waarvoor al een volume bestaat.

* Oorzaak:   
Het maken van het token (exportpad) dat u hebt opgegeven tijdens het maken van het volume is al gekoppeld aan een ander volume. 
* Oplossing:   
Kies een ander aanmaak token.  U kunt ook het andere volume verwijderen.

***Het opgegeven token voor maken is gereserveerd***

Deze fout treedt op wanneer u een volume probeert te maken en u ' default ' of ' none ' opgeeft als het bestandspad (aanmaak token).

* Oorzaak:    
U probeert een volume te maken en u geeft "default" of "none" op als het bestandspad (aanmaak token).
* Oplossing:   
Kies een ander bestandspad (token voor maken).
 
***Active Directory referenties worden gebruikt***

Deze fout treedt op wanneer u probeert de Active Directory configuratie te verwijderen van een account waarbij ten minste één SMB-volume nog bestaat.  Het SMB-volume is gemaakt met behulp van de Active Directory configuratie die u probeert te verwijderen.

* Oorzaak:   
U probeert de Active Directory configuratie van een account te verwijderen, maar er is ten minste één SMB-volume nog steeds aanwezig dat oorspronkelijk is gemaakt met behulp van de Active Directory-configuratie. 
* Oplossing:   
Verwijder eerst alle SMB-volumes die zijn gemaakt met behulp van de Active Directory configuratie.  Probeer de configuratie vervolgens opnieuw te verwijderen.

***Kan de toewijzing van de organisatie-eenheid niet wijzigen als de referenties in gebruik zijn***

Deze fout treedt op wanneer u de organisatie-eenheid van een Active Directory configuratie probeert te wijzigen, maar er nog ten minste één SMB-volume bestaat.  Het SMB-volume is gemaakt met behulp van die Active Directory configuratie die u probeert te verwijderen.

* Oorzaak:   
U probeert de organisatie-eenheid van een Active Directory configuratie te wijzigen.  Maar er bestaat nog minstens één SMB-volume dat oorspronkelijk is gemaakt met behulp van de Active Directory configuratie.
* Oplossing:   
 Verwijder eerst alle SMB-volumes die zijn gemaakt met behulp van de Active Directory configuratie.  Probeer de configuratie vervolgens opnieuw te verwijderen. 

***Active Directory update wordt al uitgevoerd***

Deze fout treedt op wanneer u een Active Directory configuratie probeert te bewerken waarvoor al een bewerkings bewerking wordt uitgevoerd.

* Oorzaak:   
U probeert een Active Directory configuratie te bewerken, maar er wordt al een andere bewerking uitgevoerd.
* Oplossing:   
Wacht tot de actieve bewerkings bewerking is voltooid.

***Alle volumes met de geselecteerde referenties eerst verwijderen***

Deze fout treedt op wanneer u een Active Directory configuratie probeert te verwijderen, maar er nog ten minste één SMB-volume bestaat.  Het SMB-volume is gemaakt met behulp van de Active Directory configuratie die u probeert te verwijderen.

* Oorzaak:   
U probeert een Active Directory configuratie te verwijderen, maar er is ten minste één SMB-volume nog aanwezig dat oorspronkelijk is gemaakt met behulp van de Active Directory-configuratie.
* Oplossing:   
Verwijder eerst alle SMB-volumes die zijn gemaakt met behulp van de Active Directory configuratie.  Probeer de configuratie vervolgens opnieuw te verwijderen. 

***Er zijn geen Active Directory referenties gevonden in de regio***

Deze fout treedt op wanneer u een SMB-volume probeert te maken, maar er is geen Active Directory configuratie toegevoegd aan het account voor de regio.

* Oorzaak:   
U probeert een SMB-volume te maken, maar er is geen Active Directory configuratie aan het account toegevoegd. 
* Oplossing:   
Voeg een Active Directory configuratie toe aan het account voordat u een SMB-volume maakt.

***Kan geen query uitvoeren op DNS-server. Controleer of de netwerk configuratie juist is en of de DNS-servers beschikbaar zijn.***

Deze fout treedt op wanneer u een SMB-volume probeert te maken, maar er is geen DNS-server (opgegeven in uw Active Directory configuratie) onbereikbaar. 

* Oorzaak:   
U probeert een SMB-volume te maken, maar er is geen DNS-server (opgegeven in de Active Directory configuratie) onbereikbaar.
* Oplossing:   
Controleer uw Active Directory configuratie en zorg ervoor dat de IP-adressen van de DNS-server juist en bereikbaar zijn.
Als er geen problemen zijn met de IP-adressen van de DNS-server, controleert u of de toegang wordt geblokkeerd door firewalls.

***Te veel gelijktijdige taken***

Deze fout treedt op wanneer u een moment opname probeert te maken wanneer er al drie andere bewerkingen voor het maken van moment opnamen worden uitgevoerd voor het abonnement.

* Oorzaak:   
U probeert een moment opname te maken wanneer er al drie andere bewerkingen voor het maken van moment opnamen worden uitgevoerd voor het abonnement. 
* Oplossing:   
Het maken van een moment opname van taken kan een paar seconden duren.  Wacht een paar seconden en voer de bewerking voor het maken van de moment opname opnieuw uit.

***Er kunnen geen extra taken worden geproduceerd. Wacht tot de lopende taken zijn voltooid en probeer het opnieuw***

Deze fout kan optreden wanneer u een volume wilt maken of verwijderen onder specifieke omstandigheden.

* Oorzaak:   
U probeert een volume te maken of verwijderen onder specifieke omstandigheden.
* Oplossing:   
Wacht een paar minuten en voer de bewerking opnieuw uit.

***Er wordt al een volume overgang tussen de statussen***

Deze fout kan optreden wanneer u probeert een volume te verwijderen dat zich momenteel in een overgangs status bevindt (dat wil zeggen, momenteel in de status maken, bijwerken of verwijderen).

* Oorzaak:   
U probeert een volume te verwijderen dat zich momenteel in een overgangs status bevindt.
* Oplossing:   
Wacht tot de bewerking die actief is (status overgang) is voltooid en voer de bewerking opnieuw uit.

***Het splitsen van het nieuwe volume van de moment opname van het bron volume is mislukt***

 Deze fout kan optreden wanneer u probeert een volume te maken op basis van een moment opname.  

* Oorzaak:   
U probeert een volume te maken op basis van een moment opname en het volume eindigt op een fout status.
* Oplossing:   
Verwijder het volume en voer de bewerking voor het maken van het volume uit van de moment opname opnieuw uit.

 
## <a name="next-steps"></a>Volgende stappen

* [Ontwikkelen voor Azure NetApp Files met REST API](azure-netapp-files-develop-with-rest-api.md)
