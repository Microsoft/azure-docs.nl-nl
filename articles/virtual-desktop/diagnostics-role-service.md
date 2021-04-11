---
title: Problemen met Windows Virtual Desktop vaststellen-Azure
description: De Windows-functie diagnostische gegevens over virtueel bureau blad gebruiken om problemen te diagnosticeren.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cf654f07cbbd729fac8feb9e2f8f7ceff471ae6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446992"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Problemen met Windows virtueel bureau blad identificeren en vaststellen

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Virtueel bureau blad van Windows biedt een functie voor diagnostische gegevens waarmee de beheerder problemen kan identificeren via één interface. Zie [log Analytics gebruiken voor de functie diagnostische gegevens voor](diagnostics-log-analytics.md)meer informatie over de diagnostische mogelijkheden van Windows virtueel bureau blad.

Verbindingen die zich niet in het virtuele bureau blad van Windows bevinden, worden niet weer gegeven in de resultaten van diagnostische gegevens omdat de functie Service diagnostiek zelf deel uitmaakt van Windows virtueel bureau blad. Er kunnen problemen met de Windows-verbinding met virtueel bureau blad optreden wanneer de eind gebruiker problemen met de netwerk verbinding ondervindt.

## <a name="common-error-scenarios"></a>Veelvoorkomende foutscenario's

In de WVDErrors-tabel worden fouten bijgehouden voor alle typen activiteiten. De kolom met de naam ' ServiceError ' levert een extra vlag met de waarde ' True ' of ' false '. Met deze markering wordt aangegeven of de fout is gerelateerd aan de service.

* Als de waarde ' True ' is, heeft het service team mogelijk al het probleem onderzocht. Als dit van invloed is op de gebruikers ervaring en een groot aantal keer wordt weer gegeven, raden we u aan om een ondersteunings ticket in te dienen voor het virtuele bureau blad van Windows.
* Als de waarde is ingesteld op ' false ', is dit mogelijk een onjuiste configuratie die u zelf kunt oplossen. Het fout bericht geeft u een aanwijzing over waar u moet beginnen.

De volgende tabel bevat algemene fouten die door uw beheerders kunnen worden uitgevoerd.

>[!NOTE]
>Deze lijst bevat de meest voorkomende fouten en wordt bijgewerkt op een gewone uitgebracht. Om ervoor te zorgen dat u de meest recente informatie hebt, moet u dit artikel ten minste één keer per maand opnieuw controleren.

## <a name="management-errors"></a>Beheer fouten

|Foutbericht|Voorgestelde oplossing|
|---|---|
|Kan de registratie sleutel niet maken |Registratie token kan niet worden gemaakt. Probeer het opnieuw te maken met een kortere verloop tijd (tussen 1 uur en 1 maand). |
|Kan de registratie sleutel niet verwijderen|Het registratie token kan niet worden verwijderd. Probeer de app opnieuw te verwijderen. Als het nog steeds niet werkt, gebruikt u Power shell om te controleren of het token nog steeds aanwezig is. Als dat het geval is, kunt u deze verwijderen met Power shell.|
|Kan de verwerkings modus voor de sessie-host niet wijzigen |De afvoer modus kan niet worden gewijzigd op de VM. Controleer de status van de virtuele machine. Als de virtuele machine niet beschikbaar is, kan de afvoer modus niet worden gewijzigd.|
|Kan geen verbinding met gebruikers sessies verbreken |De verbinding van de gebruiker met de virtuele machine is mislukt. Controleer de status van de virtuele machine. Als de virtuele machine niet beschikbaar is, kan de gebruikers sessie niet worden verbroken. Als de virtuele machine beschikbaar is, controleert u de status van de gebruikers sessie om te zien of de verbinding is verbroken. |
|Kan niet alle gebruiker (s) in de sessiehost afmelden |Kan de gebruikers niet aanmelden bij de virtuele machine. Controleer de status van de virtuele machine. Als deze niet beschikbaar is, kunnen gebruikers niet worden afgemeld. Controleer de status van de gebruikers sessie om te zien of ze al zijn afgemeld. U kunt afmelden met Power shell afdwingen. |
|Kan de toewijzing van de gebruiker aan de toepassings groep niet opheffen|Kan de publicatie van een app-groep voor een gebruiker niet ongedaan maken. Controleer of er een gebruiker beschikbaar is in azure AD. Controleer of de gebruiker deel uitmaakt van een gebruikers groep waarvan de app-groep wordt gepubliceerd. |
|Er is een fout opgetreden bij het ophalen van de beschik bare locaties |Controleer de locatie van de virtuele machine die wordt gebruikt in de wizard hostgroep maken. Als de installatie kopie niet beschikbaar is op die locatie, voegt u een installatie kopie toe op die locatie of kiest u een andere VM-locatie. |

### <a name="connection-error-codes"></a>Verbindings fout codes

|Numerieke code|Foutcode|Voorgestelde oplossing|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|De sessiehost is niet juist gekoppeld aan de Active Directory.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|De verbindingen zijn mislukt omdat de sessiehost niet beschikbaar is. Controleer de status van de sessie-host.|
|-2146233088|ConnectionFailedClientDisconnect|Als deze fout regel matig wordt weer geven, controleert u of de computer van de gebruiker is verbonden met het netwerk.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|De sessie waarmee de host-gebruiker probeerde verbinding te maken, is niet in orde. Fouten opsporen in de virtuele machine.|
|-2146233088|ConnectionFailedUserNotAuthorized|De gebruiker is niet gemachtigd om toegang te krijgen tot de gepubliceerde app of het bureau blad. De fout kan worden weer gegeven nadat de beheerder gepubliceerde resources heeft verwijderd. Vraag de gebruiker om de feed in de Extern bureaublad toepassing te vernieuwen.|
|2|FileNotFound|De toepassing waartoe de gebruiker toegang probeerde te krijgen, is onjuist geïnstalleerd of ingesteld op een onjuist pad.<br><br>Bij het publiceren van nieuwe apps terwijl de gebruiker een actieve sessie heeft, heeft de gebruiker geen toegang tot deze app. De sessie moet worden afgesloten en opnieuw worden gestart voordat de gebruiker toegang kan krijgen tot de app. |
|3|InvalidCredentials|De gebruikers naam of het wacht woord die de gebruiker heeft ingevoerd, komt niet overeen met de bestaande gebruikers namen of wacht woorden. Controleer de referenties voor type fouten en probeer het opnieuw.|
|8|ConnectionBroken|De verbinding tussen de client en de gateway of server is verbroken. Er is geen actie vereist, tenzij deze onverwacht plaatsvindt.|
|14|UnexpectedNetworkDisconnect|De verbinding met het netwerk is verbroken. Vraag de gebruiker om opnieuw verbinding te maken.|
|24|ReverseConnectFailed|De virtuele machine van de host heeft geen directe regel van het gezichts vermogen om te RD-gateway. Zorg ervoor dat het IP-adres van de gateway kan worden omgezet.|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>Fout: kan geen gebruikers toewijzingen toevoegen aan een app-groep

Nadat u een gebruiker aan een app-groep hebt toegewezen, wordt in de Azure Portal een waarschuwing weer gegeven waarin staat dat de sessie eindigt of verificatie problemen ondervindt-extensie Microsoft_Azure_WVD. De pagina toewijzing wordt vervolgens niet geladen en vervolgens stopt het laden van pagina's in de Azure Portal (bijvoorbeeld Azure Monitor, Log Analytics, Service Health enzovoort).

**Oorzaak:** Er is een probleem met het beleid voor voorwaardelijke toegang. De Azure Portal probeert een token te verkrijgen voor Microsoft Graph, dat afhankelijk is van share point online. De klant heeft een beleid voor voorwaardelijke toegang met de naam ' Microsoft Office 365 gebruiks voorwaarden voor gegevens opslag ' waarvoor gebruikers de gebruiks voorwaarden voor toegang tot de gegevens opslag moeten accepteren. Ze hebben zich echter nog niet aangemeld, dus het Azure Portal kan het token niet ophalen.

**Oplossen:** Voordat u zich aanmeldt bij de Azure Portal, moet de beheerder zich eerst aanmelden bij share point en akkoord gaan met de gebruiks voorwaarden. Daarna moeten ze zich kunnen aanmelden bij de Azure Portal zoals normaal.

## <a name="next-steps"></a>Volgende stappen

Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de functies van virtuele Bureau bladen van Windows.

Zie de [Power shell-referentie](/powershell/windows-virtual-desktop/overview)voor een lijst met beschik bare Power shell-cmdlets voor virtuele Windows-Bureau bladen.
