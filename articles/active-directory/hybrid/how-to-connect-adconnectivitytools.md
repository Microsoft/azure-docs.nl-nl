---
title: 'Azure AD Connect: wat is de ADConnectivityTool Power shell-module | Microsoft Docs'
description: In dit document wordt de nieuwe ADConnectivity Power shell-module geïntroduceerd en wordt uitgelegd hoe u deze kunt gebruiken om problemen op te lossen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973453"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Problemen met Azure AD-connectiviteit oplossen met de Power shell-module ADConnectivityTool

Het hulp programma ADConnectivity is een Power shell-module die wordt gebruikt in een van de volgende:

- Tijdens de installatie van een probleem met een netwerk verbinding wordt voor komen dat de Active Directory referenties die de gebruiker in de wizard heeft gegeven, correct worden gevalideerd.
- Post-installatie door een gebruiker die de functies aanroept vanuit een Power shell-sessie.

Het hulp programma bevindt zich in: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool. psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool tijdens de installatie

Als er een netwerk probleem optreedt op de pagina **verbinding maken met uw adres lijsten** Azure AD Connect, gebruikt de ADConnectivityTool automatisch een van de functies om te bepalen wat er gebeurt.  Een van de volgende kan worden beschouwd als netwerk problemen:

- De naam van het forest dat door de gebruiker is opgegeven, is onjuist getypt of het forest bestaat niet 
- UDP-poort 389 is gesloten op de domein controllers die zijn gekoppeld aan het forest dat door de gebruiker is gegeven
- De referenties die in het venster ' AD-forest-account ' zijn opgenomen, hebben geen bevoegdheden voor het ophalen van de domein controllers die zijn gekoppeld aan de doel-forest
- Een van de TCP-poorten 53, 88 of 389 is gesloten op de domein controllers die zijn gekoppeld aan het forest dat de gebruiker heeft gegeven 
- UDP 389 en een TCP-poort (of poorten) zijn gesloten
- Kan DNS niet omzetten voor het gegeven forest en/of de bijbehorende domein controllers

Wanneer een van deze problemen wordt gevonden, wordt er een verwant fout bericht weer gegeven in de wizard AADConnect:


![Fout](media/how-to-connect-adconnectivitytools/error1.png)

Als we bijvoorbeeld proberen een directory toe te voegen op het scherm **verbinding maken met uw mappen** , moet Azure AD Connect dit controleren en verwacht te kunnen communiceren met een domein controller via poort 389.  Als dat niet het geval is, ziet u de fout die wordt weer gegeven in de bovenstaande scherm afbeelding.  

Wat er daad werkelijk gebeurt achter de schermen, is dat Azure AD Connect de functie aanroept `Start-NetworkConnectivityDiagnosisTools` .  Deze functie wordt aangeroepen wanneer de validatie van de referenties mislukt vanwege een probleem met de netwerk verbinding.

Ten slotte wordt er een gedetailleerd logboek bestand gegenereerd wanneer het hulp programma wordt aangeroepen vanuit de wizard. Het logboek bevindt zich in **C:\ProgramData\AADConnect\ADConnectivityTool- \<date> - \<time> . log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools na installatie
Nadat Azure AD Connect is geïnstalleerd, kunnen alle functies in de Power shell-module ADConnectivityTools worden gebruikt.  

U kunt referentie-informatie vinden over de functies in de [ADConnectivityTools-verwijzing](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

We gaan deze functie aanroepen omdat deze **alleen** hand matig kan worden aangeroepen nadat de ADConnectivityTool. Psm1 in Power shell is geïmporteerd. 

Deze functie voert dezelfde logica uit die de Azure AD Connect wizard uitvoert om de gegeven AD-referenties te valideren.  Het biedt echter een veel uitgebreidere uitleg over het probleem en een voorgestelde oplossing. 

De connectiviteits validatie bestaat uit de volgende stappen:
-   Domein-FQDN (Fully Qualified Domain Name) object ophalen
-   Controleer of, als de gebruiker ' nieuwe AD-account maken ' is geselecteerd, deze referenties deel uitmaken van de groep Ondernemings Administrators
-   FQDN-object van forest ophalen
-   Controleer of ten minste één domein dat is gekoppeld aan het eerder verkregen forest-FQDN-object bereikbaar is
-   Controleer of het functionaliteits niveau van het forest Windows Server 2003 of hoger is.

De gebruiker kan een map toevoegen als al deze acties zijn uitgevoerd.

Als de gebruiker deze functie uitvoert nadat een probleem is opgelost (of als er geen probleem is), wordt in de uitvoer aangegeven dat de gebruiker terug moet gaan naar de wizard Azure AD Connect en vervolgens de referenties opnieuw kunt invoegen.



## <a name="next-steps"></a>Volgende stappen
- [Azure AD Connect: accounts en machtigingen](reference-connect-accounts-permissions.md)
- [Snelle installatie](how-to-connect-install-express.md)
- [Aangepaste installatie](how-to-connect-install-custom.md)
- [ADConnectivityTools-verwijzing](reference-connect-adconnectivitytools.md)

