---
title: 'Azure AD Connect: write-back van apparaat inschakelen | Microsoft Docs'
description: Dit document bevat informatie over het inschakelen van write-back van apparaten met behulp van Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95973419"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: write-back van apparaat inschakelen
> [!NOTE]
> Er is een abonnement op Azure AD Premium vereist voor het terugschrijven van het apparaat.
> 
> 

De volgende documentatie bevat informatie over het inschakelen van de functie voor het terugschrijven van apparaten in Azure AD Connect. Write-back van apparaat wordt gebruikt in de volgende scenario's:

* [Windows hello voor bedrijven inschakelen met behulp van hybride certificaat vertrouwens implementatie](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Schakel voorwaardelijke toegang op basis van apparaten in op AD FS (2012 R2 of hoger) beveiligde toepassingen (Relying Party-vertrouwens relaties).

Dit biedt extra beveiliging en zekerheid dat toegang tot toepassingen alleen wordt verleend aan vertrouwde apparaten. Zie [Risico's beheren met voorwaardelijke toegang](../conditional-access/overview.md) en [on-premises voorwaardelijke toegang instellen met behulp van Azure Active Directory Device Registration](../devices/overview.md)voor meer informatie over voorwaardelijke toegang.

> [!IMPORTANT]
> <li>Apparaten moeten zich in hetzelfde forest bevinden als de gebruikers. Omdat apparaten moeten worden teruggeschreven naar één forest, ondersteunt deze functie momenteel geen implementatie met meerdere gebruikers forests.</li>
> <li>Er kan slechts één configuratie-object voor apparaatregistratie worden toegevoegd aan het on-premises Active Directory-forest. Deze functie is niet compatibel met een topologie waarin de on-premises Active Directory worden gesynchroniseerd met meerdere Azure AD-mappen.</li>

## <a name="part-1-install-azure-ad-connect"></a>Deel 1: Azure AD Connect installeren
Installeer Azure AD Connect met aangepaste of snelle instellingen. Micro soft raadt aan om te beginnen met alle gebruikers en groepen die zijn gesynchroniseerd voordat u write-back van apparaat inschakelt.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Deel 2: write-back van apparaat inschakelen in Azure AD Connect
1. Voer de installatie wizard opnieuw uit. Selecteer **Opties voor apparaat configureren** op de pagina extra taken en klik op **volgende**. 

    ![Opties voor apparaten configureren](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > De nieuwe opties voor het configureren van apparaten zijn alleen beschikbaar in versie 1.1.819.0 en nieuwer.

2. Selecteer op de pagina apparaat-opties de optie **write-back van apparaat configureren**. De optie om het **terugschrijven van apparaten uit te scha kelen** , is pas beschikbaar als write-back van apparaat is ingeschakeld. Klik op **volgende** om naar de volgende pagina in de wizard te gaan.
    ![Bewerking apparaat kiezen](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Op de pagina write-back wordt het opgegeven domein weer gegeven als het standaard apparaat voor het terugschrijven van apparaten.
   ![Doel-forest voor het terugschrijven van het aangepaste installatie apparaat](./media/how-to-connect-device-writeback/writebackforest.png)

4. Pagina **container van apparaat** biedt de mogelijkheid om de Active Directory voor te bereiden met een van de twee beschik bare opties:

    a. **Referenties voor ondernemings Administrator opgeven**: als de referenties van de ondernemings beheerder worden verstrekt voor het forest waar de apparaten opnieuw moeten worden geschreven, wordt het forest automatisch door Azure AD Connect bereid tijdens de configuratie van het terugschrijven van het apparaat.

    b. **Power shell-script downloaden**: Azure AD Connect automatisch een Power shell-script genereert waarmee de Active Directory voor het terugschrijven van apparaten kan worden voor bereid. Als de referenties van de ondernemings beheerder niet zijn opgenomen in Azure AD Connect, wordt u aangeraden het Power shell-script te downloaden. Geef de gedownloade Power shell-script **CreateDeviceContainer.ps1** op naar de ondernemings beheerder van het forest waarnaar apparaten worden teruggeschreven.
    ![Active Directory-forest voorbereiden](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    De volgende bewerkingen worden uitgevoerd om het Active Directory-forest voor te bereiden:
    * Als ze nog niet bestaan, maakt en configureert u nieuwe containers en objecten onder CN = apparaatregistratie configuratie, CN = Services, CN = Configuratie, [forest-DN].
    * Als ze nog niet bestaan, maakt en configureert u nieuwe containers en objecten onder CN = RegisteredDevices, [domein-DN]. Apparaat-objecten worden in deze container gemaakt.
    * Hiermee stelt u de benodigde machtigingen voor het Azure AD-Connector account in om apparaten op uw Active Directory te beheren.
    * Er hoeft alleen te worden uitgevoerd op één forest, zelfs als Azure AD Connect wordt geïnstalleerd op meerdere forests.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Controleren of apparaten zijn gesynchroniseerd met Active Directory
Write-back van apparaat moet nu goed werken. Houd er rekening mee dat het Maxi maal drie uur kan duren voordat de Device-objecten naar AD zijn geschreven.  Ga als volgt te werk om te controleren of uw apparaten correct zijn gesynchroniseerd:

1. Start het Active Directory-beheercentrum.
2. Vouw RegisteredDevices uit binnen het domein dat federatief is.

   ![Active Directory beheer centrum geregistreerde apparaten](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. De huidige geregistreerde apparaten worden hier weer gegeven.

   ![Lijst met geregistreerde apparaten van Active Directory-beheer centrum](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Voorwaardelijke toegang inschakelen
Gedetailleerde instructies voor het inschakelen van dit scenario zijn beschikbaar in het [instellen van on-premises voorwaardelijke toegang met behulp van Azure Active Directory Device Registration](../devices/overview.md).

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="the-writeback-checkbox-is-still-disabled"></a>Het selectie vakje terugschrijven is nog steeds uitgeschakeld
Als het selectie vakje voor het terugschrijven van apparaten niet is ingeschakeld ondanks dat u de bovenstaande stappen hebt gevolgd, wordt u door de volgende stappen begeleid bij het controleren van de installatie wizard voordat het selectie vakje is ingeschakeld.

Eerste zaken:

* Het forest waarin de apparaten aanwezig zijn, moet zijn bijgewerkt naar het niveau Windows 2012 R2, zodat het apparaatobject en de bijbehorende kenmerken aanwezig zijn.
* Als de installatie wizard al wordt uitgevoerd, worden er geen wijzigingen gedetecteerd. In dit geval voltooit u de installatie wizard en voert u deze opnieuw uit.
* Zorg ervoor dat het account dat u opgeeft in het initialisatie script in feite de juiste gebruiker is die door de Active Directory-connector wordt gebruikt. Voer de volgende stappen uit om dit te controleren:
  * Open de **synchronisatie service** vanuit het menu Start.
  * Open het tabblad **connectors** .
  * Zoek de connector met het type Active Directory Domain Services en selecteer deze.
  * Onder **acties**, selecteert u **Eigenschappen**.
  * Ga naar **verbinding maken met Active Directory forest**. Controleer of het domein en de gebruikers naam in dit scherm overeenkomen met het account dat is opgegeven aan het script.
    ![Connector account in Sync Service Manager](./media/how-to-connect-device-writeback/connectoraccount.png)

Controleer de configuratie in Active Directory:

* Controleer of de Device Registration service zich bevindt op de volgende locatie (CN = DeviceRegistrationService, CN = Device Registration Services, CN = apparaatregistratie-configuratie, CN = Services, CN = Configuratie) onder configuratie naamgevings context.

![Problemen oplossen, DeviceRegistrationService in de configuratie naam ruimte](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Controleer of er slechts één configuratie object is door de configuratie naam ruimte te doorzoeken. Als er meer dan één is, verwijder dan het duplicaat.

![Problemen oplossen, zoeken naar dubbele objecten](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Zorg ervoor dat het kenmerk msDS-DeviceLocation aanwezig is en een waarde heeft op het Device Registration service-object. Zoek deze locatie op en zorg ervoor dat deze aanwezig is met het object type msDS-DeviceContainer.

![Problemen oplossen, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Problemen oplossen, RegisteredDevices-object klasse](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Controleer of het account dat wordt gebruikt door de Active Directory Connector de vereiste machtigingen heeft voor de container geregistreerde apparaten die door de vorige stap is gevonden. Dit zijn de verwachte machtigingen voor deze container:

![Problemen oplossen, machtigingen controleren voor container](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Controleer of het Active Directory account machtigingen heeft voor de configuratie van CN = apparaatregistratie, CN = Services, CN = configuratie object.

![Problemen oplossen, machtigingen controleren bij configuratie van apparaatregistratie](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Aanvullende informatie
* [Risico's beheren met voorwaardelijke toegang](../conditional-access/overview.md)
* [On-premises voorwaardelijke toegang instellen met behulp van Azure Active Directory Device Registration](../devices/overview.md)

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).