---
title: Problemen met Enterprise State Roaming in Azure Active Directory oplossen
description: Hier vindt u antwoorden op enkele vragen die beheerders kunnen hebben over instellingen en app-gegevens synchronisatie.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: df70891ef090d44769aadbc235273e3193bc780e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837206"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Problemen met Enterprise State Roaming instellingen in Azure Active Directory oplossen

In dit onderwerp vindt u informatie over het oplossen en diagnosticeren van problemen met Enterprise State Roaming en vindt u een lijst met bekende problemen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Dit artikel is van toepassing op de op HTML gebaseerde browser van micro soft Edge die is geïntroduceerd in Windows 10 in juli 2015. Het artikel is niet van toepassing op de nieuwe micro soft Edge-browser op basis van chroom, uitgebracht op 15 januari 2020. Zie het artikel [micro soft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)(Engelstalig) voor meer informatie over het synchronisatie gedrag voor de nieuwe micro soft Edge.

## <a name="preliminary-steps-for-troubleshooting"></a>Voorbereidende stappen voor het oplossen van problemen 

Voordat u begint met het oplossen van problemen, controleert u of de gebruiker en het apparaat correct zijn geconfigureerd en of het apparaat en de gebruiker voldoen aan de vereisten van Enterprise State Roaming. 

1. Windows 10, met de meest recente updates en een minimum versie 1511 (OS Build 10586 of hoger) is geïnstalleerd op het apparaat. 
1. Het apparaat is toegevoegd aan Azure AD of hybride Azure AD. Zie [een apparaat verkrijgen onder het beheer van Azure AD](overview.md)voor meer informatie.
1. Zorg ervoor dat **Enterprise State roaming** is ingeschakeld voor de Tenant in azure AD, zoals wordt beschreven in [om Enterprise State roaming in te scha kelen](enterprise-state-roaming-enable.md). U kunt roaming inschakelen voor alle gebruikers of voor alleen een geselecteerde groep gebruikers.
1. Aan de gebruiker is een Azure Active Directory Premium-licentie toegewezen.  
1. Het apparaat moet opnieuw worden opgestart en de gebruiker moet zich opnieuw aanmelden om toegang te krijgen tot Enterprise State Roaming functies.

## <a name="information-to-include-when-you-need-help"></a>Benodigde informatie om snel te worden geholpen
Als u het probleem niet kunt oplossen met de onderstaande instructies, neemt u contact op met onze ondersteunings medewerkers. Wanneer u contact met hen opneemt, neemt u de volgende informatie op:

* **Algemene beschrijving van de fout**: zijn er fout berichten die door de gebruiker worden weer gegeven? Als er geen fout bericht wordt weer gegeven, beschrijf dan het onverwachte gedrag dat u in detail hebt gezien. Welke functies zijn ingeschakeld voor synchronisatie en wat is de verwachte synchronisatie van de gebruiker? Worden er meerdere functies die niet worden gesynchroniseerd of zijn ze geïsoleerd voor één?
* **Betrokken gebruikers** : is synchroniseren/mislukken voor één gebruiker of meerdere gebruikers? Hoeveel apparaten zijn er betrokken bij per gebruiker? Zijn ze niet synchroon of worden ze gesynchroniseerd en worden sommige niet gesynchroniseerd?
* **Informatie over de gebruiker** : welke identiteit is de gebruiker die wordt gebruikt om u aan te melden bij het apparaat? Hoe wordt de gebruiker aangemeld bij het apparaat? Zijn ze onderdeel van een geselecteerde beveiligings groep die kan worden gesynchroniseerd? 
* **Informatie over het apparaat** : dit apparaat is lid van Azure AD en is lid van een domein? Welke build is het apparaat? Wat zijn de meest recente updates?
* **Datum/tijd/tijds zone** – wat is de exacte datum en tijd waarop u de fout hebt gezien (Neem de tijd zone op)?

Met inbegrip van deze informatie kan ons uw probleem zo snel mogelijk oplossen.

## <a name="troubleshooting-and-diagnosing-issues"></a>Het oplossen en diagnosticeren van problemen

In deze sectie vindt u meer informatie over het oplossen van problemen met Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Controleer de synchronisatie en de instellingen pagina uw instellingen synchroniseren 

1. Nadat u uw Windows 10-PC hebt toegevoegd aan een domein dat is geconfigureerd om Enterprise State Roaming toe te staan, meldt u zich aan met uw werk account. Ga naar **instellingen**  >  **accounts**  >  **Synchroniseer uw instellingen** en controleer of de synchronisatie en de afzonderlijke instellingen zijn ingeschakeld en of de bovenkant van de pagina instellingen aangeeft dat u synchroniseert met uw werk account. Bevestig dat hetzelfde account ook wordt gebruikt als uw aanmeldings account in **instellingen**  >  **accounts**  >  **uw gegevens**. 
1. Controleer of de synchronisatie werkt op meerdere computers door enkele wijzigingen aan te brengen op de oorspronkelijke computer, zoals het verplaatsen van de taak balk naar de rechter-of bovenkant van het scherm. Bekijk de wijziging in de tweede computer binnen vijf minuten. 

   * Het vergren delen en ontgrendelen van het scherm (Win + L) kan helpen bij het activeren van een synchronisatie.
   * U moet zich op beide Pc's aanmelden met hetzelfde account als Enterprise State Roaming is gekoppeld aan het gebruikers account en niet op het computer account.

**Mogelijk probleem**: als de besturings elementen op de pagina **instellingen** niet beschikbaar zijn en u het bericht ' sommige Windows-onderdelen zijn alleen beschikbaar als u een Microsoft-account of een werk account gebruikt ' ziet. Dit probleem kan zich voordoen voor apparaten die zijn ingesteld om te worden toegevoegd aan een domein en die zijn geregistreerd bij Azure AD, maar het apparaat is nog niet met succes geverifieerd naar Azure AD. Een mogelijke oorzaak is dat het apparaatbeleid moet worden toegepast, maar deze toepassing wordt asynchroon uitgevoerd en kan enkele uren worden uitgesteld. 

### <a name="verify-the-device-registration-status"></a>De registratie status van het apparaat controleren

Enterprise State Roaming vereist dat het apparaat wordt geregistreerd bij Azure AD. Hoewel het niet specifiek is voor Enterprise State Roaming, kunt u aan de hand van de onderstaande instructies controleren of de Windows 10-client is geregistreerd en bevestigen de vinger afdruk, URL van Azure AD-instellingen, NGC-status en andere informatie.

1. Open de opdracht prompt zonder verhoogde bevoegdheden. Als u dit wilt doen in Windows, opent u het start programma (Win + R) en typt u cmd om te openen.
1. Zodra de opdracht prompt is geopend, typt u '*dsregcmd.exe/status*'.
1. Voor de verwachte uitvoer moet de waarde van het veld **AzureAdJoined** ' Yes ' zijn, **WamDefaultSet** veld waarde moet ' Yes ' zijn en de waarde van het **WAMDEFAULTGUID** -veld moet een GUID zijn met ' (AzureAd) ' aan het einde.

**Mogelijk probleem**: **WamDefaultSet** en **AzureAdJoined** hebben beide ' no ' in de veld waarde, het apparaat is toegevoegd aan een domein en is geregistreerd bij Azure AD, en het apparaat wordt niet gesynchroniseerd. Als dit wordt weer gegeven, moet het apparaat mogelijk wachten totdat het beleid is toegepast of de verificatie van het apparaat is mislukt bij het maken van verbinding met Azure AD. Het kan zijn dat de gebruiker enkele uren moet wachten voordat het beleid wordt toegepast. Andere stappen voor het oplossen van problemen zijn onder andere het opnieuw proberen van de herregistratie door u af te melden en weer in te schrijven, of door de taak te starten in taak planner. In sommige gevallen kunt u '*dsregcmd.exe/Leave*' uitvoeren in een opdracht prompt venster met verhoogde bevoegdheid, het systeem opnieuw opstarten en opnieuw proberen de registratie mogelijk te maken.

**Mogelijk probleem**: het veld voor **SettingsUrl** is leeg en het apparaat wordt niet gesynchroniseerd. De gebruiker is mogelijk voor het laatst aangemeld bij het apparaat voordat Enterprise State Roaming was ingeschakeld in de Azure Active Directory Portal. Start het apparaat opnieuw op en laat de gebruiker zich aanmelden. Probeer eventueel in de Portal de IT-beheerder naar **Azure Active Directory**  >  **apparaten** te navigeren  >  **Enterprise State roaming** **gebruikers kunnen instellingen en app-gegevens op meerdere apparaten synchroniseren** en weer inschakelen. Zodra het opnieuw is ingeschakeld, start u het apparaat opnieuw op en laat u de gebruiker zich aanmelden. Als het probleem hiermee niet is opgelost, kan **SettingsUrl** leeg zijn als er een beschadigd apparaat is. In dit geval kunt u '*dsregcmd.exe/Leave*' uitvoeren in een opdracht prompt venster met verhoogde bevoegdheid, de computer opnieuw opstarten en opnieuw proberen de registratie te helpen.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming en Multi-Factor Authentication 

Onder bepaalde omstandigheden kan Enterprise State Roaming gegevens niet synchroniseren als Azure AD Multi-Factor Authentication is geconfigureerd. Zie het document [KB3193683](https://support.microsoft.com/kb/3193683)voor ondersteuning voor meer informatie over deze symptomen. 

**Mogelijk probleem**: als uw apparaat zo is geconfigureerd dat multi-factor Authentication op de Azure Active Directory Portal vereist is, kunt u de instellingen niet synchroniseren tijdens het aanmelden bij een Windows 10-apparaat met een wacht woord. Dit type Multi-Factor Authentication configuratie is bedoeld om een Azure Administrator-account te beveiligen. Gebruikers met beheerders rechten kunnen nog steeds synchroniseren door zich aan te melden bij hun Windows 10-apparaten met hun Microsoft Passport for Work pincode of door Multi-Factor Authentication in te vullen bij het openen van andere Azure-Services, zoals Microsoft 365.

**Mogelijk probleem**: de synchronisatie kan mislukken als de beheerder de Active Directory Federation Services multi-factor Authentication beleid voor voorwaardelijke toegang configureert en het toegangs token op het apparaat is verlopen. Zorg ervoor dat u zich aanmeldt en u zich afmeldt met behulp van de Microsoft Passport for Work pincode of volt ooien Multi-Factor Authentication tijdens het openen van andere Azure-Services zoals Microsoft 365.

### <a name="event-viewer"></a>Logboeken

Voor geavanceerde probleem oplossing kunt u Logboeken gebruiken om specifieke fouten te vinden. Deze worden beschreven in de volgende tabel. De gebeurtenissen vindt u onder Logboeken > **toepassingen en services**  >  **micro soft**  >  **Windows**  >  **SettingSync-Azure** en voor identiteits problemen met synchronisatie **toepassingen en-services registreert**  >  **micro soft**  >  **Windows**  >  **Aad**.

## <a name="known-issues"></a>Bekende problemen

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronisatie werkt niet op apparaten waarop apps zijn geladen met MDM-software

Beïnvloedt apparaten met de Windows 10 jubileum update (versie 1607). In Logboeken onder de SettingSync-Azure-Logboeken wordt gebeurtenis-ID 6013 met fout 80070259 vaak weer gegeven.

**Aanbevolen actie**  
Zorg ervoor dat de Windows 10 v1607-client de cumulatieve update van 23 augustus 2016 ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393,82) heeft. 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Favorieten van Internet Explorer worden niet gesynchroniseerd

Beïnvloedt apparaten waarop de update voor Windows 10 november (versie 1511) wordt uitgevoerd.

**Aanbevolen actie**  
Zorg ervoor dat de Windows 10 v1511-client de cumulatieve update van juli 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586,494) heeft.

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Het thema wordt niet gesynchroniseerd, evenals gegevens die zijn beveiligd met Windows Information Protection 

Om te voor komen dat gegevens lekken, worden gegevens die zijn beveiligd met [Windows Information Protection](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) , niet gesynchroniseerd via Enterprise State roaming voor apparaten met de update voor Windows 10 jubileum.

**Aanbevolen actie**  
Geen. In toekomstige updates voor Windows is dit probleem mogelijk opgelost.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>De datum-, tijd-en regio-instellingen worden niet gesynchroniseerd op een apparaat dat lid is van een domein 
  
Voor apparaten die lid zijn van een domein, wordt de instellings datum, tijd en regio niet automatisch gesynchroniseerd. Het gebruik van automatische tijd kan de andere datum-, tijd-en regio-instellingen overschrijven en ervoor zorgen dat de instellingen niet worden gesynchroniseerd. 

**Aanbevolen actie**  
Geen. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC vraagt bij het synchroniseren van wacht woorden

Beïnvloedt apparaten waarop de update van Windows 10 november (versie 1511) wordt uitgevoerd met een draadloze NIC die is geconfigureerd voor het synchroniseren van wacht woorden.

**Aanbevolen actie**  
Zorg ervoor dat de Windows 10 v1511-client de cumulatieve update ([KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586,494) heeft.

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronisatie werkt niet op apparaten die gebruikmaken van een Smart Card voor aanmelding

Als u zich probeert aan te melden bij uw Windows-apparaat met behulp van een Smart Card of virtuele Smart Card, werkt de synchronisatie van instellingen niet meer.     

**Aanbevolen actie**  
Geen. In toekomstige updates voor Windows is dit probleem mogelijk opgelost.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Een apparaat dat lid is van een domein wordt niet gesynchroniseerd nadat het bedrijfs netwerk is verlaten     

Apparaten die lid zijn van een domein en die zijn geregistreerd bij Azure AD, kunnen een synchronisatie fout ondervinden als het apparaat gedurende lange tijd een site uitvalt en de domein authenticatie niet kan worden voltooid.

**Aanbevolen actie**  
Verbind het apparaat met een bedrijfs netwerk, zodat de synchronisatie kan worden hervat.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Het gekoppelde Azure AD-apparaat wordt niet gesynchroniseerd en de gebruiker heeft een User Principal name met een gemengde case.

Als de gebruiker een gemengde UPN heeft (bijvoorbeeld gebruikers naam in plaats van gebruikers naam) en de gebruiker zich op een aan Azure AD gekoppelde apparaat bevindt dat is bijgewerkt van Windows 10 build 10586 naar 14393, kan het apparaat van de gebruiker niet worden gesynchroniseerd. 

**Aanbevolen actie**  
De gebruiker moet het apparaat ontkoppelen en opnieuw toevoegen aan de Cloud. Als u dit wilt doen, meldt u zich aan als de lokale beheerders gebruiker en ontkoppelt u het apparaat door naar het **instellingen**  >  **systeem**  >  **over** te gaan en ' beheren of verbinding met werk of school verbreken ' te selecteren. Ruim de onderstaande bestanden op en probeer Azure AD vervolgens opnieuw toe te voegen in het **instellingen**  >  **systeem**  >  **over** en selecteer verbinding maken met werk of school. Ga door met het toevoegen van het apparaat aan Azure Active Directory en voltooi de stroom.

Ruim de volgende bestanden op in de opschoon stap:
- Settings. dat in `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Alle bestanden in de map `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Gebeurtenis-ID 6065:80070533 deze gebruiker kan zich niet aanmelden omdat dit account momenteel is uitgeschakeld  

In Logboeken onder de logboeken SettingSync/debug kan deze fout worden weer gegeven wanneer de referenties van de gebruiker zijn verlopen. Daarnaast kan het gebeuren wanneer de Tenant niet automatisch AzureRMS ingericht heeft. 

**Aanbevolen actie**  
In het eerste geval moet de gebruiker de referenties bijwerken en zich aanmelden bij het apparaat met de nieuwe referenties. Als u het probleem met AzureRMS wilt oplossen, gaat u verder met de stappen die worden vermeld in [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Gebeurtenis-ID 1098: fout: er is een 0xCAA5001C-token Broker-bewerking mislukt  

In Logboeken onder de AAD/Operation-logboeken kan deze fout worden weer gegeven met gebeurtenis 1104: het Get-resultaat van de aanroep van de AAD-Cloud-AP-invoeg toepassing is geretourneerd fout: 0xC000005F. Dit probleem treedt op als er machtigingen of eigendoms kenmerken ontbreken.  

**Aanbevolen actie**  
Ga verder met de stappen die worden weer gegeven [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van ENTER prise State roaming](enterprise-state-roaming-overview.md)voor een overzicht.