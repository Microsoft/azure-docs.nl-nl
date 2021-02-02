---
title: Toepassings inrichtings status van quarantaine | Microsoft Docs
description: Wanneer u een toepassing voor het automatisch inrichten van gebruikers hebt geconfigureerd, leest u wat een inrichtings status in quarantaine betekent en hoe u deze wist.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/24/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d997c85f96fa9f87ca6d017cb555b3732007e21c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256302"
---
# <a name="application-provisioning-in-quarantine-status"></a>Toepassing inrichten in quarantaine status

De Azure AD-inrichtings service bewaakt de status van uw configuratie. Ook worden de beschadigde apps in de status ' quarantaine ' geplaatst. Als de meeste of alle aanroepen voor het doel systeem consistent zijn, wordt de inrichtings taak gemarkeerd als in quarantaine. Een voor beeld van een fout is een fout die is ontvangen vanwege ongeldige beheerders referenties.

In quarantaine:
- De frequentie van incrementele cycli wordt geleidelijk tot eenmaal per dag gereduceerd.
- De inrichtings taak wordt uit quarantaine gehaald nadat alle fouten zijn opgelost en de volgende synchronisatie cyclus wordt gestart. 
- Als de inrichtings taak langer dan vier weken in quarantaine blijft, wordt de inrichtings taak uitgeschakeld (stopt met uitvoeren).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hoe kan ik weet of mijn toepassing in quarantaine is?

Er zijn drie manieren om te controleren of een toepassing in quarantaine is geplaatst:
  
- Ga in het Azure Portal naar **Azure Active Directory**  >  **bedrijfs toepassingen**  >  &lt; *toepassings naam* &gt;  >  **inrichten** en controleer de voortgangs balk voor een quarantaine bericht.   

  ![Status balk van de inrichting met de status van de quarantaine](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Ga in het Azure Portal naar **Azure Active Directory**  >  **controle logboeken** > filter op **activiteit: quarantaine** en controleer de quarantaine geschiedenis. In de weer gave in de voortgangs balk zoals hierboven beschreven wordt aangegeven of het inrichten momenteel in quarantaine is. In de controle Logboeken wordt de quarantaine geschiedenis voor een toepassing weer gegeven. 

- Gebruik de Microsoft Graph aanvraag [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) om programmatisch de status van de inrichtings taak op te halen:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Controleer uw e-mail adres. Wanneer een toepassing in quarantaine wordt geplaatst, wordt er een eenmalige e-mail melding verzonden. Als de quarantaine reden wordt gewijzigd, wordt een bijgewerkt e-mail bericht verzonden met de nieuwe reden voor quarantaine. Als u geen e-mail bericht ziet:

  - Zorg ervoor dat u een geldig **e-mail adres voor meldingen** hebt opgegeven in de inrichtings configuratie voor de toepassing.
  - Zorg ervoor dat er geen spam filtering is op het postvak in van de meldings-e-mail.
  - Zorg ervoor dat u uw abonnement op e-mail berichten niet hebt opgezegd.
  - Controleren op e-mails van `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Waarom wordt mijn toepassing in quarantaine geplaatst?

|Beschrijving|Aanbevolen actie|
|---|---|
|**Probleem met scim-naleving:** Er is een antwoord HTTP/404 niet gevonden geretourneerd in plaats van het verwachte HTTP/200 OK-antwoord. In dit geval heeft de Azure AD-inrichtings service een aanvraag ingediend bij de doel toepassing en heeft deze een onverwacht antwoord ontvangen.|Controleer de sectie beheerders referenties. Controleer of de toepassing de Tenant-URL moet opgeven en of de URL juist is. Als er geen probleem wordt weer geven, neemt u contact op met de ontwikkelaar van de toepassing om te controleren of hun service SCIM-compatibel is. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Ongeldige referenties:** Wanneer u probeert toestemming te verlenen voor toegang tot de doel toepassing, hebben we een reactie ontvangen van de doel toepassing die aangeeft dat de ingevoerde referenties ongeldig zijn.|Navigeer naar de sectie beheerders referenties van de gebruikers interface van de inrichtings configuratie en autoriseer Access opnieuw met geldige referenties. Als de toepassing zich in de galerie bevindt, raadpleegt u de zelf studie over de configuratie van de toepassing voor meer vereiste stappen.|
|**Dubbele rollen:** Rollen die zijn geïmporteerd uit bepaalde toepassingen, zoals Sales Force en Zendesk, moeten uniek zijn. |Navigeer naar het toepassings [manifest](../develop/reference-app-manifest.md) in het Azure Portal en verwijder de dubbele rol.|

 Een Microsoft Graph aanvraag voor het ophalen van de status van de inrichtings taak bevat de volgende reden voor quarantaine:
- `EncounteredQuarantineException` geeft aan dat er ongeldige referenties zijn geleverd. De inrichtings service kan geen verbinding tot stand brengen tussen het bron systeem en het doel systeem.
- `EncounteredEscrowProportionThreshold` geeft aan dat het inrichten de borg drempel heeft overschreden. Dit probleem treedt op wanneer meer dan 40% van de inrichtings gebeurtenissen mislukt. Zie voor meer informatie borg drempelwaarde details hieronder.
- `QuarantineOnDemand` betekent dat we een probleem met uw toepassing hebben gedetecteerd en hand matig hebben ingesteld op quarantaine.

**Borg drempels**

Als aan de proportionele borg drempel wordt voldaan, gaat de inrichtings taak in quarantaine. Deze logica kan worden gewijzigd, maar dit komt van pas zoals hieronder wordt beschreven: 

Een taak kan in quarantaine worden geplaatst, ongeacht het aantal fouten voor problemen zoals beheerders referenties of SCIM-naleving. In het algemeen zijn 5.000-fouten echter het minimum om te beginnen of ze in quarantaine moeten worden geplaatst wegens te veel storingen. Een taak met 4.000 fouten gaat bijvoorbeeld niet in quarantaine. Maar een taak met 5.000 fouten zou een evaluatie activeren. Een evaluatie maakt gebruik van de volgende criteria:  
- Als er meer dan 40% van de inrichtings gebeurtenissen mislukken of er meer dan 40.000 fouten zijn, gaat de inrichtings taak in quarantaine. Referentie fouten worden niet geteld als onderdeel van de drempel waarde van 40% of 40.000. Als er bijvoorbeeld een fout is opgetreden bij het bijwerken van een manager of een groepslid, mislukt de verwijzing.
- Een taak waarbij 45.000 gebruikers niet met succes zijn ingericht, zou leiden tot quarantaine omdat deze de drempel waarde voor 40.000 overschrijdt.
- Een taak waarbij de inrichting van 30.000 gebruikers die zijn mislukt en 5.000 geslaagd zou zijn in quarantaine, omdat deze de limiet van 40% en 5.000 overschrijdt.
- Een taak met 20.000 fouten en 100.000 succes gaat niet in quarantaine, omdat het wegwerk niet meer dan de drempel waarde van 40% overschrijdt of het maximum aantal 40.000 is mislukt.  
- Er is een absolute drempel van 60.000 fouten die zijn opgetreden voor zowel referentie-als niet-referentie fouten. 40.000 gebruikers kunnen bijvoorbeeld niet worden ingericht en 21.000 Manager-updates zijn mislukt. Het totaal is 61.000 fouten en overschrijdt de limiet van 60.000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hoe kan ik mijn toepassing uit quarantaine halen?

Los eerst het probleem op dat ertoe heeft geleid dat de toepassing in quarantaine is geplaatst.

- Controleer de inrichtings instellingen van de toepassing om er zeker van te zijn dat u [geldige beheerders referenties hebt ingevoerd](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD moet een vertrouwens relatie met de doel toepassing tot stand brengen. Zorg ervoor dat u geldige referenties hebt ingevoerd en dat uw account over de benodigde machtigingen beschikt.

- Controleer de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md) om te onderzoeken welke fouten in quarantaine worden veroorzaakt en los de fout op. Ga naar **Azure Active Directory** &gt; **Enter prise apps** &gt; **-inrichtings Logboeken (preview)** in het gedeelte **activiteit** .

Nadat u het probleem hebt opgelost, start u de inrichtings taak opnieuw. Bepaalde wijzigingen in de inrichtings instellingen van de toepassing, zoals kenmerk toewijzingen of bereik filters, worden automatisch opnieuw opgestart. De voortgangs balk op de **inrichtings** pagina van de toepassing geeft aan wanneer het inrichten voor het laatst is gestart. Als u de inrichtings taak hand matig opnieuw moet starten, gebruikt u een van de volgende methoden:  

- Gebruik de Azure Portal om de inrichtings taak opnieuw te starten. Op de **inrichtings** pagina van de toepassing onder **instellingen** selecteert u **status wissen en start u de synchronisatie opnieuw** en stelt u de **inrichtings status** **in op aan**. Met deze actie wordt de inrichtings service volledig opnieuw gestart. Dit kan enige tijd duren. Er wordt opnieuw een volledige eerste cyclus uitgevoerd. Hiermee wist u de toepassing, worden de apps uit quarantaine verwijderd en worden eventuele water merken gewist.

- Gebruik Microsoft Graph om [de inrichtings taak opnieuw te starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). U hebt volledige controle over wat u opnieuw opstart. U kunt ervoor kiezen om de borg te wissen (om de borg teller voor de quarantaine status opnieuw te starten), de quarantaine te wissen (de toepassing uit quarantaine te verwijderen) of door water merken te wissen. Gebruik de volgende aanvraag:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Vervang {ID} door de waarde van de toepassings-ID en vervang {jobId} door de [id van de synchronisatie taak](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
