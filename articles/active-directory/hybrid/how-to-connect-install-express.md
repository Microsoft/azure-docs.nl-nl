---
title: 'Azure AD Connect: aan de slag met expresinstellingen | Microsoft Docs'
description: In dit artikel kunt u lezen hoe u de installatiewizard downloadt, installeert en uitvoert voor Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a655f355bb77d937f4daff2f8987769416ebd8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279666"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Aan de slag met Azure AD Connect met expresinstellingen
**Expresinstellingen** van Azure AD Connect worden gebruikt wanneer u een singleforesttopologie hebt en [synchronisatie van wachtwoord-hash](how-to-connect-password-hash-synchronization.md) voor verificatie. **Expresinstellingen** is de standaardoptie en wordt gebruikt voor het meest geïmplementeerde scenario. U bent slechts enkele snelle klikken verwijderd van uitbreiding van uw on-premises directory naar de cloud.

Zorg ervoor dat u, voordat u begint met de installatie van Azure AD Connect, [Azure AD Connect downloadt](https://go.microsoft.com/fwlink/?LinkId=615771) en de vereiste stappen in [Azure AD Connect: Hardware and prerequisites](how-to-connect-install-prerequisites.md) voltooit.

Zie [verwante documentatie](#related-documentation) voor andere scenario's als de expresinstellingen niet met uw topologie overeenkomen.

## <a name="express-installation-of-azure-ad-connect"></a>Snelle installatie van Azure AD Connect
In de sectie met [video's](#videos) kunt u zien hoe deze stappen in de praktijk worden uitgevoerd.

1. Meld u aan als lokale beheerder op de server waarop u Azure AD Connect wilt installeren. Doe dit op de server die u wilt gebruiken als synchronisatieserver.
2. Ga naar **AzureADConnect.msi** en dubbelklik erop.
3. Selecteer in het welkomstscherm het vakje waarmee u aangeeft akkoord te gaan met de licentievoorwaarden en klik op **Doorgaan**.  
4. Klik in het scherm Expresinstellingen op **Expresinstellingen gebruiken**.  
   ![Welkom bij Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. Voer in het scherm Verbinding maken met Azure AD de gebruikersnaam en het wachtwoord in van een hoofdbeheerder voor Azure AD. Klik op **Volgende**.  
   ![Verbinding maken met Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   Zie [Connectiviteitsproblemen oplossen](tshoot-connect-connectivity.md) als u een foutbericht krijgt en u problemen hebt met de connectiviteit.
6. Voer in het scherm Verbinding maken met Azure AD de gebruikersnaam en het wachtwoord in voor een enterprisebeheerdersaccount. U kunt het domeingedeelte in NetBios- of FQDN-indeling invoeren, dat wil zeggen FABRIKAM\administrator of fabrikam.com\administrator. Klik op **Volgende**.  
   ![Verbinding maken met AD DS](./media/how-to-connect-install-express/connectad.png)
7. De pagina [**Configuratie van aanmelding bij Azure AD**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) wordt alleen weergegeven als u de [domeinen niet hebt geverifieerd](../fundamentals/add-custom-domain.md) bij de [vereisten](how-to-connect-install-prerequisites.md).
   ![Niet-geverifieerde domeinen](./media/how-to-connect-install-express/unverifieddomain.png)  
   Als deze pagina wordt weergegeven, controleert u elk domein dat is gemarkeerd met **Niet toegevoegd** en **Niet geverifieerd**. Zorg ervoor dat de domeinen die u gebruikt in Azure AD zijn geverifieerd. Klik op het symbool Vernieuwen wanneer u uw domeinen hebt geverifieerd.
8. Klik in het venster Gereed om te configureren op **Installeren**.
   * Optioneel kunt u op de pagina Gereed voor configuratie het vakje **Start het synchronisatieproces zodra de configuratie is voltooid** uitschakelen. Schakel dit selectievakje uit als u een aanvullende configuratie wilt uitvoeren, zoals [filteren](how-to-connect-sync-configure-filtering.md). Als u deze optie uitschakelt, wordt de synchronisatie geconfigureerd met de wizard, maar blijft de planner uitgeschakeld. Deze wordt niet uitgevoerd tenzij u deze handmatig inschakelt door de [installatiewizard opnieuw uit te voeren](how-to-connect-installation-wizard.md).
   * Als u het selectievakje **Start het synchronisatieproces zodra de configuratie is voltooid** ingeschakeld laat, wordt er onmiddellijk een volledige synchronisatie van alle gebruikers, groepen en contactpersonen in Azure AD geactiveerd.
   * Als u Exchange hebt ingeschakeld in uw on-premises Active Directory, hebt u ook een optie voor het inschakelen van [**Hybride implementatie voor Exchange**](/exchange/exchange-hybrid). Schakel deze optie in als u van plan bent Exchange-postvakken tegelijkertijd in de cloud en on-premises te gebruiken.
     ![Klaar voor het configureren van Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. Wanneer de installatie is voltooid, klikt u op **Afsluiten**.
10. Nadat de installatie is voltooid, dient u zich af te melden en weer aan te melden voordat u de Synchronization Service Manager of Synchronization Rule Editor gaat gebruiken.

## <a name="videos"></a>Video's
Voor een video over het gebruik van expresinstallatie gaat u naar:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>Volgende stappen
Nu u Azure AD Connect geïnstalleerd hebt, kunt u [de installatie verifiëren en licenties toewijzen](how-to-connect-post-installation.md).

Lees meer over deze functies, die tijdens de installatie zijn ingeschakeld: [Automatische upgrade](how-to-connect-install-automatic-upgrade.md), [Onopzettelijk verwijderen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md) en [Azure AD Connect Health](how-to-connect-health-sync.md).

Lees meer over deze veelvoorkomende onderwerpen: [Scheduler en het activeren van de synchronisatie](how-to-connect-sync-feature-scheduler.md).

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>Verwante documentatie

| Onderwerp | Koppeling |
| --- | --- |
| Overzicht Azure AD Connect | [Uw on-premises directory's integreren met Azure Active Directory](whatis-hybrid-identity.md)
| Installeren met behulp van aangepaste instellingen | [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md) |
| Upgrade van DirSync | [Upgrade van Azure AD-synchronisatiehulpprogramma (DirSync) (Engelstalig artikel)](how-to-dirsync-upgrade-get-started.md)|
| Accounts die worden gebruikt voor installatie | [Meer informatie over referenties en machtigingen van Azure AD Connect](reference-connect-accounts-permissions.md) |