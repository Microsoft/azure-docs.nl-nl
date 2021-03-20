---
title: Back-ups van accounts maken en herstellen met de Microsoft Authenticator-app-Azure AD
description: Meer informatie over het maken van een back-up en het herstellen van uw back-upaccountgegevens met behulp van de app Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 06/03/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: e7608196c962a6918a90b91457f856f3e11b04f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91530898"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Back-up en herstel van account referenties met behulp van de app Microsoft Authenticator

**Van toepassing op:**

- iOS-apparaten met versie 5.7.0 en hoger

- Android-apparaten met versie 6.6.0 en hoger

De Microsoft Authenticator-app maakt een back-up van uw account referenties en gerelateerde app-instellingen, zoals de volg orde van uw accounts, naar de Cloud. Na het maken van een back-up kunt u de app ook gebruiken om uw gegevens op een nieuw apparaat te herstellen, waardoor het voor komen van vergrendelde of het opnieuw maken van accounts.

Voor elke opslag locatie voor back-ups moet u één persoonlijk Microsoft-account hebben, terwijl iOS ook een iCloud-account nodig heeft. U kunt meerdere accounts op die ene locatie opslaan. U kunt bijvoorbeeld een persoonlijk account, een werk-of school account hebben en een persoonlijk, niet-Microsoft-account zoals voor Facebook, Google, enzovoort.

> [!IMPORTANT]
> Alleen de referenties van uw persoonlijke en derde account worden opgeslagen, inclusief uw gebruikers naam en de verificatie code van de account die is vereist om uw identiteit te bewijzen. Er worden geen andere gegevens opgeslagen die zijn gekoppeld aan uw accounts, met inbegrip van e-mail berichten of bestanden. We koppelen of delen uw accounts ook niet op enigerlei wijze of met andere producten of services. En ten slotte krijgt de IT-beheerder geen informatie over een van deze accounts.

## <a name="back-up-your-account-credentials"></a>Back-up maken van uw account referenties

Voordat u een back-up van uw referenties kunt maken, hebt u het volgende nodig:

- Een persoonlijke [Microsoft-account](https://account.microsoft.com/account) om als uw herstel account te fungeren.

- **Alleen voor IOS** moet u een iCloud- [account](https://www.icloud.com/) hebben voor de werkelijke opslag locatie.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Cloud back-up inschakelen voor iOS-apparaten

- Selecteer op uw iOS-apparaat **instellingen**, selecteer **back-up** en schakel vervolgens **iCloud-back-up** in.

    Er wordt een back-up van uw account referenties gemaakt op uw iCloud-account.

    ![scherm iOS-instellingen, waarin de locatie van de iCloud-back-upinstellingen wordt weer gegeven](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Cloud back-up inschakelen voor Android-apparaten

- Selecteer op uw Android-apparaat **instellingen**, selecteer **back-up** en schakel vervolgens **Cloud back-up** in.

    Er wordt een back-up van uw account referenties gemaakt op uw Cloud account.

    ![Scherm Android-instellingen, waarin de locatie van de back-upinstellingen wordt weer gegeven](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Uw account referenties herstellen op het nieuwe apparaat

U kunt uw account referenties herstellen vanuit uw Cloud account, maar u moet er eerst voor zorgen dat het account dat u wilt herstellen, niet aanwezig is in de app Microsoft Authenticator. Als u bijvoorbeeld uw persoonlijke Microsoft-account herstelt, moet u ervoor zorgen dat u geen persoonlijke Microsoft-account hebt ingesteld in de verificator-app. Deze controle is van belang om ervoor te zorgen dat er niet per ongeluk een bestaand account wordt overschreven of gewist.

### <a name="to-recover-your-information"></a>Uw gegevens herstellen

1. Open op uw mobiele apparaat de Microsoft Authenticator-app en selecteer vervolgens **herstel starten** onder aan het scherm.

    ![App Microsoft Authenticator, waarin wordt weer gegeven waar u op herstel starten klikt](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Meld u aan bij uw herstel account met dezelfde persoonlijke Microsoft-account die u hebt gebruikt tijdens het back-upproces.

    Uw account referenties worden hersteld naar het nieuwe apparaat.

Nadat u uw herstel hebt voltooid, ziet u mogelijk dat uw persoonlijke Microsoft-account verificatie codes in de Microsoft Authenticator-app verschillen tussen uw oude en nieuwe telefoons. De codes verschillen, omdat elk apparaat een eigen unieke referentie heeft, maar beide geldig zijn en werken tijdens het aanmelden met behulp van de bijbehorende telefoon.

## <a name="recover-accounts-requiring-more-verification"></a>Accounts herstellen waarvoor meer verificatie is vereist

Als u push meldingen met uw persoonlijke werk-of school account gebruikt, ontvangt u een waarschuwing op het scherm met de melding dat u aanvullende verificatie moet opgeven voordat u uw gegevens kunt herstellen. Omdat push meldingen vereisen dat een referentie wordt gebruikt die is gekoppeld aan uw specifieke apparaat en nooit via het netwerk wordt verzonden, moet u uw identiteit bewijzen voordat de referentie op uw apparaat wordt gemaakt.

Voor persoonlijke micro soft-accounts kunt u uw identiteit bewijzen door uw wacht woord in te voeren en een alternatief e-mail adres of telefoon nummer. Voor werk-of school accounts moet u een QR-code scannen die u hebt ontvangen van uw account provider.

### <a name="to-provide-more-verification-for-personal-accounts"></a>Meer verificatie voor persoonlijke accounts

1. Tik in het scherm **accounts** van de Microsoft Authenticator-app op het account dat u wilt herstellen om de weer gave volledig scherm van het account te openen.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png" alt-text="Scherm opname van de Microsoft Authenticator-app met de beschik bare account tegels." border="true":::

1. Tik op de tegel voor het account dat u wilt herstellen en tik vervolgens op de optie om u aan te melden om te herstellen. Voer uw wacht woord in en bevestig uw e-mail adres of telefoon nummer als aanvullende verificatie.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/backup-and-recovery-codes.png" alt-text="Scherm opname waarin de Microsoft Authenticator eenmalige wachtwoord code wordt weer gegeven." border="true":::

### <a name="to-provide-more-verification-for-work-or-school-accounts"></a>Meer verificatie voor werk-of school accounts

1. Tik in het scherm **accounts** van de Microsoft Authenticator-app op het account dat u wilt herstellen om de weer gave volledig scherm van het account te openen.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-arrow.png" alt-text="Microsoft Authenticator-app, waarin de beschik bare account tegels worden weer gegeven" border="true":::

1. Tik in de weer gave volledig scherm op de optie om een QR-code te scannen om deze volledig te herstellen.

    :::image type="content" source="media/user-help-auth-app-backup-recovery/work-or-school-recovery-qr-code.png" alt-text="Verificator presenteert een eenmalig wacht woord als een verificatie code" border="true":::

>[!NOTE]
>Voor meer informatie over QR-codes en hoe u er een kunt krijgen, raadpleegt u aan [de slag met de Microsoft Authenticator-app](./user-help-auth-app-download-install.md) of [beveiligings gegevens instellen voor het gebruik van een verificator-app](./security-info-setup-auth-app.md), op basis van het feit of uw beheerder beveiligings gegevens heeft ingeschakeld.
>
>Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of de wilt toestaan foto's te maken en video op te nemen (Android). U moet **Toestaan** selecteren, zodat de Authenticator-app toegang heeft tot uw camera om in de volgende stap een foto te maken van de QR-code. Als u de app geen cameratoegang geeft, kunt u de Authenticator-app wel installeren, maar moet u de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md) voor informatie over het handmatig toevoegen van de code.

## <a name="troubleshoot-backup-and-recovery-problems"></a>Problemen met back-up en herstel oplossen

Er zijn een aantal redenen waarom uw back-up mogelijk niet beschikbaar is

- **Besturings systemen wijzigen**: uw back-up wordt opgeslagen in de ICloud voor IOS en in de Cloud-opslag provider van micro soft voor Android. Dit betekent dat uw back-up niet beschikbaar is als u overschakelt tussen Android-en iOS-apparaten. Als u de switch maakt, moet u uw accounts hand matig opnieuw maken binnen de app Microsoft Authenticator.

- **Netwerk problemen**: als u problemen met het netwerk ondervindt, controleert u of u verbinding hebt met het netwerk en of u bent aangemeld bij uw account.

- **Account problemen**: als u problemen ondervindt met het account, moet u ervoor zorgen dat u bent aangemeld bij uw account. Voor iOS betekent dit dat u moet zijn aangemeld bij iCloud met hetzelfde Apple-account als uw iPhone.

- **Onbedoeld verwijderen**: het is mogelijk dat u uw back-upaccount hebt verwijderd van het vorige apparaat of tijdens het beheren van uw opslag account voor de Cloud. In dit geval moet u uw account hand matig opnieuw in de app maken.

- **Bestaande Microsoft Authenticator accounts**: als u al accounts hebt ingesteld in de Microsoft Authenticator app, kan de app uw back-upaccount niet herstellen. Door herstel te voor komen, zorgt u ervoor dat uw account gegevens niet worden overschreven met verouderde informatie. In dit geval moet u de bestaande account gegevens verwijderen uit de bestaande accounts die in de verificator-app zijn ingesteld voordat u uw back-up kunt herstellen.

- De **back-up is verouderd**: als uw back-upgegevens verouderd zijn, wordt u mogelijk gevraagd om de gegevens te vernieuwen door u opnieuw aan te melden bij uw micro soft Recovery-account. Uw herstel account is de persoonlijke Microsoft-account die u in eerste instantie hebt gebruikt om uw back-up op te slaan. Als een aanmelding vereist is, ziet u een rode stip in het menu of de actie balk, of ziet u een uitroep teken pictogram waarin u wordt gevraagd om u aan te melden om het terugzetten vanuit een back-up te volt ooien. Nadat u het juiste pictogram hebt geselecteerd, wordt u gevraagd om u opnieuw aan te melden om uw gegevens bij te werken.

## <a name="next-steps"></a>Volgende stappen

Nu u een back-up hebt gemaakt en uw account referenties hebt hersteld naar uw nieuwe apparaat, kunt u de app Microsoft Authenticator blijven gebruiken om uw identiteit te verifiëren. Zie [Aanmelden bij uw accounts met behulp van de app Microsoft Authenticator](user-help-sign-in.md)voor meer informatie.

## <a name="related-articles"></a>Verwante artikelen:

- [Wat is de Microsoft Authenticator-app?](user-help-auth-app-overview.md)

- [Veelgestelde vragen over Microsoft Authenticator-app](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](/azure/multi-factor-authentication/)