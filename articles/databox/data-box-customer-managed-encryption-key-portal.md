---
title: Gebruik de Azure Portal voor het beheren van door de klant beheerde sleutels voor Azure Data Box
description: Meer informatie over het gebruik van Azure Portal om door de klant beheerde sleutels te maken en te beheren met Azure Key Vault voor een Azure Data Box. Met door de klant beheerde sleutels kunt u toegangs beheer maken, draaien, uitschakelen en intrekken.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: cd9f4ad6b6831b2b15c09b37edc569b3f2d247f7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958079"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Door de klant beheerde sleutels gebruiken in Azure Key Vault voor Azure Data Box

Azure Data Box beveiligt de ontgrendelings sleutel van het apparaat (ook wel het wacht woord van het apparaat genoemd), dat wordt gebruikt om een apparaat te vergren delen via een versleutelings sleutel. Deze versleutelings sleutel is standaard een door micro soft beheerde sleutel. Voor extra controle kunt u een door de klant beheerde sleutel gebruiken.

Het gebruik van een door de klant beheerde sleutel heeft geen invloed op de manier waarop gegevens op het apparaat worden versleuteld. Dit is alleen van invloed op hoe de ontgrendelings sleutel van het apparaat is versleuteld.

Gebruik een door de klant beheerde sleutel wanneer u een bestelling maakt om dit controle niveau in het hele bestel proces te houden. Zie [zelf studie: Order Azure data Box](data-box-deploy-ordered.md)voor meer informatie.

In dit artikel wordt uitgelegd hoe u een door de klant beheerde sleutel kunt inschakelen voor uw bestaande Data Box order in de [Azure Portal](https://portal.azure.com/). U leert hoe u de sleutel kluis, de sleutel, de versie of de identiteit wijzigt voor uw huidige door de klant beheerde sleutel, of u kunt teruggaan naar het gebruik van een door micro soft beheerde sleutel.

Dit artikel is van toepassing op Azure Data Box en Azure Data Box Heavy apparaten.

## <a name="requirements"></a>Vereisten

De door de klant beheerde sleutel voor een Data Box order moet voldoen aan de volgende vereisten:

- De sleutel moet worden gemaakt en opgeslagen in een Azure Key Vault met **voorlopig verwijderen** en **niet wissen** ingeschakeld. Zie [Wat is Azure Key Vault?](../key-vault/general/overview.md) voor meer informatie. U kunt een sleutel kluis en sleutel maken terwijl u uw bestelling maakt of bijwerkt.

- De sleutel moet een RSA-sleutel zijn van 2048 of groter.

## <a name="enable-key"></a>Sleutel inschakelen

Voer de volgende stappen uit als u een door de klant beheerde sleutel wilt inschakelen voor uw bestaande Data Box order in de Azure Portal:

1. Ga naar het scherm **overzicht** voor uw data Box order.

    ![Overzichts scherm van een Data Box order-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Ga naar **instellingen > versleuteling** en selecteer door de **klant beheerde sleutel**. Selecteer vervolgens **een sleutel en sleutel kluis selecteren**.

    ![Selecteer de door de klant beheerde sleutel versleutelings optie](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Uw abonnement wordt automatisch ingevuld op het scherm **sleutel selecteren in azure Key Vault** .

 3. Voor **sleutel kluis** kunt u een bestaande sleutel kluis selecteren in de vervolg keuzelijst of **nieuwe maken** en een nieuwe sleutel kluis maken.

     ![Opties voor sleutel kluis bij het selecteren van een door de klant beheerde sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Als u een nieuwe sleutel kluis wilt maken, voert u het abonnement, de resource groep, de naam van de sleutel kluis en andere informatie in het scherm **nieuwe sleutel kluis maken** in. Zorg ervoor dat in **herstel opties de optie** voor het tijdelijk **verwijderen** en leegmaken van de **beveiliging** is ingeschakeld. Selecteer vervolgens **Controleren en maken**.

      ![Azure Key Vault controleren en maken](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Controleer de informatie voor uw sleutel kluis en selecteer **maken**. Wacht enkele minuten tot het maken van de sleutel kluis is voltooid.

       ![Azure Key Vault met uw instellingen maken](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. U kunt in het scherm **sleutel selecteren van Azure Key Vault** een bestaande sleutel selecteren in de sleutel kluis of een nieuwe maken.

    ![Selecteer een sleutel in Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Als u een nieuwe sleutel wilt maken, selecteert u **nieuwe maken**. U moet een RSA-sleutel gebruiken. De grootte kan 2048 of hoger zijn.

    ![Nieuwe sleutel maken in Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Voer een naam in voor de nieuwe sleutel, accepteer de andere standaard waarden en selecteer **maken**. U wordt gewaarschuwd dat er een sleutel is gemaakt in uw sleutel kluis.

    ![Naam nieuwe sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. Voor **versie** kunt u een bestaande sleutel versie selecteren in de vervolg keuzelijst.

    ![Selecteer een versie voor de nieuwe sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Als u een nieuwe sleutel versie wilt genereren, selecteert u **nieuwe maken**.

    ![Een dialoog venster openen voor het maken van een nieuwe sleutel versie](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Kies instellingen voor de nieuwe sleutel versie en selecteer **maken**.

    ![Een nieuwe sleutel versie maken](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. Wanneer u een sleutel kluis, sleutel en sleutel versie hebt geselecteerd, kiest u **selecteren**.

    ![Een sleutel in een Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    In de instellingen voor het **versleutelings type** worden de sleutel kluis en de sleutel weer gegeven die u hebt gekozen.

    ![Sleutel en sleutel kluis voor een door de klant beheerde sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Selecteer het type identiteit dat moet worden gebruikt voor het beheren van de door de klant beheerde sleutel voor deze resource. U kunt de door het **systeem toegewezen** identiteit gebruiken die tijdens het maken van de order is gegenereerd of een door de gebruiker toegewezen identiteit kiezen.

    Een door de gebruiker toegewezen identiteit is een onafhankelijke resource die u kunt gebruiken om de toegang tot resources te beheren. Zie [beheerde identiteits typen](/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie.

    ![Het identiteits type selecteren](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Als u een gebruikers identiteit wilt toewijzen, selecteert u **gebruiker toegewezen**. Selecteer vervolgens **een gebruikers identiteit selecteren** en selecteer de beheerde identiteit die u wilt gebruiken.

    ![Een te gebruiken identiteit selecteren](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    U kunt hier geen nieuwe gebruikers-id maken. Zie een [rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)als u wilt weten hoe u er een kunt maken.

    De geselecteerde gebruikers-id wordt weer gegeven in de instellingen voor het **versleutelings type** .

    ![Een geselecteerde gebruikers-id die wordt weer gegeven in de instellingen voor versleutelings type](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Selecteer **Opslaan** om de bijgewerkte instellingen voor het **versleutelings type** op te slaan.

     ![Sla uw door de klant beheerde sleutel op](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    De sleutel-URL wordt weer gegeven onder **versleutelings type**.

    ![Door de klant beheerde sleutel-URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Sleutel wijzigen

Voer de volgende stappen uit om de sleutel kluis, de sleutel en/of de sleutel versie te wijzigen voor de door de klant beheerde sleutel die u momenteel gebruikt:

1. Ga in het venster **overzicht** voor uw data Box order naar **instellingen**  >  **versleuteling** en klik op **sleutel wijzigen**.

    ![Overzichts scherm van een Data Box order met door de klant beheerde sleutel-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Kies **een andere sleutel kluis en sleutel selecteren**.

    ![Overzichts scherm van een Data Box order, selecteer een andere sleutel en een optie voor de sleutel kluis](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. In het scherm **sleutel selecteren op basis van sleutel kluis ziet u** het abonnement, maar geen sleutel kluis, sleutel of sleutel versie. U kunt een van de volgende wijzigingen aanbrengen:

   - Selecteer een andere sleutel in dezelfde sleutel kluis. U moet de sleutel kluis selecteren voordat u de sleutel en versie selecteert.

   - Selecteer een andere sleutel kluis en wijs een nieuwe sleutel toe.

   - De versie voor de huidige sleutel wijzigen.
   
    Wanneer u klaar bent met de wijzigingen, kiest u **selecteren**.

    ![Versleutelings optie kiezen-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Selecteer **Opslaan**.

    ![Bijgewerkte versleutelings instellingen opslaan-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Identiteit wijzigen

Voer de volgende stappen uit om de identiteit te wijzigen die wordt gebruikt om de toegang tot de door de klant beheerde sleutel voor deze order te beheren:

1. Ga in het venster **overzicht** voor de voltooide Data Box order naar **instellingen**  >  **versleuteling**.

2. Breng een van de volgende wijzigingen aan:

     - Als u wilt overschakelen naar een andere gebruikers-id, klikt u op **een andere gebruikers identiteit selecteren**. Selecteer vervolgens een andere identiteit in het deel venster aan de rechter kant van het scherm en kies **selecteren**.

       ![Optie voor het wijzigen van de door de gebruiker toegewezen identiteit voor een door de klant beheerde sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Als u wilt overschakelen naar de door het systeem toegewezen identiteit die tijdens het maken van de order is gegenereerd, selecteert u **systeem toegewezen** door **identiteits type selecteren**.

     ![Optie voor het wijzigen van een systeem dat is toegewezen aan een door de klant beheerde sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Selecteer **Opslaan**.

    ![Bijgewerkte versleutelings instellingen opslaan-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Door micro soft beheerde sleutel gebruiken

Ga als volgt te werk om een door de klant beheerde sleutel te wijzigen in de micro soft-beheerde sleutel voor uw bestelling:

1. Ga in het venster **overzicht** voor de voltooide Data Box order naar **instellingen**  >  **versleuteling**.

2. Selecteer bij **type** de optie **micro soft Managed Key**.

    ![Overzichts scherm van een Data Box order-5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Selecteer **Opslaan**.

    ![Bijgewerkte versleutelings instellingen opslaan voor een door micro soft beheerde sleutel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Problemen oplossen

Als u fouten met betrekking tot uw door de klant beheerde sleutel ontvangt, gebruikt u de volgende tabel om problemen op te lossen.

| Fout code| Foutdetails| Herstel bare?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel is uitgeschakeld.| Ja, door de sleutel versie in te scha kelen.|
| SsemUserErrorEncryptionKeyExpired| Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel is verlopen.| Ja, door de sleutel versie in te scha kelen.|
| SsemUserErrorKeyDetailsNotFound| Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel niet is gevonden.| Als u de sleutel kluis hebt verwijderd, kunt u de door de klant beheerde sleutel niet herstellen.  Als u de sleutel kluis naar een andere Tenant hebt gemigreerd, raadpleegt u [een sleutel kluis Tenant-id wijzigen nadat een abonnement is verplaatst](../key-vault/general/move-subscription.md). Als u de sleutel kluis hebt verwijderd:<ol><li>Ja, als het zich in de duur van de schone beveiliging bevindt, gebruikt u de stappen in [een sleutel kluis herstellen](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nee, als het na de duur van de schone beveiliging valt.</li></ol><br>Als de sleutel kluis een Tenant migratie heeft ontvangen, ja, kan deze worden hersteld met behulp van een van de volgende stappen: <ol><li>Herstel de sleutel kluis terug naar de oude Tenant.</li><li>Stel `Identity = None` in en stel vervolgens de waarde weer in op `Identity = SystemAssigned` . Hiermee wordt de identiteit verwijderd en opnieuw gemaakt zodra de nieuwe identiteit is gemaakt. Inschakelen `Get` , `Wrap` , en `Unwrap` machtigingen voor de nieuwe identiteit in het toegangs beleid van de sleutel kluis.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Er is een door de klant beheerde sleutel toegepast, maar de toegang tot de sleutel is niet toegestaan of ingetrokken of er kan geen toegang worden verkregen tot de sleutel kluis omdat de firewall is ingeschakeld. | Voeg de geselecteerde identiteit toe aan uw sleutel kluis om toegang tot de door de klant beheerde sleutel in te scha kelen. Als op de sleutel kluis firewall is ingeschakeld, schakelt u over naar een door het systeem toegewezen identiteit en voegt u vervolgens een door de klant beheerde sleutel toe. Zie How to [Enable the key](#enable-key)(Engelstalig) voor meer informatie. |
| SsemUserErrorKeyVaultDetailsNotFound| Kan de sleutel niet ophalen omdat de bijbehorende sleutel kluis voor de door de klant beheerde sleutel niet is gevonden. | Als u de sleutel kluis hebt verwijderd, kunt u de door de klant beheerde sleutel niet herstellen.  Als u de sleutel kluis naar een andere Tenant hebt gemigreerd, raadpleegt u [een sleutel kluis Tenant-id wijzigen nadat een abonnement is verplaatst](../key-vault/general/move-subscription.md). Als u de sleutel kluis hebt verwijderd:<ol><li>Ja, als het zich in de duur van de schone beveiliging bevindt, gebruikt u de stappen in [een sleutel kluis herstellen](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault).</li><li>Nee, als het na de duur van de schone beveiliging valt.</li></ol><br>Als de sleutel kluis een Tenant migratie heeft ontvangen, ja, kan deze worden hersteld met behulp van een van de volgende stappen: <ol><li>Herstel de sleutel kluis terug naar de oude Tenant.</li><li>Stel `Identity = None` in en stel vervolgens de waarde weer in op `Identity = SystemAssigned` . Hiermee wordt de identiteit verwijderd en opnieuw gemaakt zodra de nieuwe identiteit is gemaakt. Inschakelen `Get` , `Wrap` , en `Unwrap` machtigingen voor de nieuwe identiteit in het toegangs beleid van de sleutel kluis.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel niet is gevonden.| Ja, controleren of: <ol><li>Sleutel kluis heeft nog steeds de MSI in het toegangs beleid.</li><li>Identiteit is van het type systeem toegewezen.</li><li>Stel Get-, wrap-en Unwrap-machtigingen in voor de identiteit in het toegangs beleid van de sleutel kluis.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Toevoegen van een nieuwe door de gebruiker toegewezen identiteit is mislukt omdat u de limiet hebt bereikt van het totale aantal door de gebruiker toegewezen identiteiten dat kan worden toegevoegd. | Voer de bewerking opnieuw uit met minder gebruikers identiteiten of verwijder een door de gebruiker toegewezen identiteiten uit de resource voordat u het opnieuw probeert. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | De toegang tot de beheerde identiteit is mislukt. <br> Opmerking: dit is voor het scenario wanneer het abonnement wordt verplaatst naar een andere Tenant. De klant moet de identiteit hand matig verplaatsen naar een nieuwe Tenant. PFA-mail voor meer informatie. | Verplaats de identiteit die is geselecteerd voor de nieuwe Tenant waarbij het abonnement aanwezig is. Zie How to [Enable the key](#enable-key)(Engelstalig) voor meer informatie. |
| SsemUserErrorKekUserIdentityNotFound | Er is een door de klant beheerde sleutel toegepast, maar de door de gebruiker toegewezen identiteit die toegang tot de sleutel heeft, is niet gevonden in de Active Directory. <br> Opmerking: dit is voor het geval wanneer de gebruikers identiteit uit Azure wordt verwijderd.| Probeer een andere door de gebruiker toegewezen identiteit toe te voegen die is geselecteerd voor de sleutel kluis om toegang tot de door de klant beheerde sleutel in te scha kelen. Zie How to [Enable the key](#enable-key)(Engelstalig) voor meer informatie. |
| SsemUserErrorUserAssignedIdentityAbsent | Kan de sleutel niet ophalen omdat de door de klant beheerde sleutel niet is gevonden. | Kan geen toegang krijgen tot de door de klant beheerde sleutel. De door de gebruiker toegewezen identiteit (UAI) die is gekoppeld aan de sleutel, wordt verwijderd of het type UAI is gewijzigd. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | De toegang tot de beheerde identiteit is mislukt. <br> Opmerking: dit is voor het scenario wanneer het abonnement wordt verplaatst naar een andere Tenant. De klant moet de identiteit hand matig verplaatsen naar een nieuwe Tenant. PFA-mail voor meer informatie. | Probeer een andere door de gebruiker toegewezen identiteit toe te voegen die is geselecteerd voor de sleutel kluis om toegang tot de door de klant beheerde sleutel in te scha kelen. Zie How to [Enable the key](#enable-key)(Engelstalig) voor meer informatie.|
| SsemUserErrorKeyVaultBadRequestException | Er is een door de klant beheerde sleutel toegepast, maar de toegang tot de sleutel is niet toegestaan of ingetrokken of er kan geen toegang worden verkregen tot de sleutel kluis omdat de firewall is ingeschakeld. | Voeg de geselecteerde identiteit toe aan uw sleutel kluis om toegang tot de door de klant beheerde sleutel in te scha kelen. Als op de sleutel kluis firewall is ingeschakeld, schakelt u over naar een door het systeem toegewezen identiteit en voegt u vervolgens een door de klant beheerde sleutel toe. Zie How to [Enable the key](#enable-key)(Engelstalig) voor meer informatie. |
| Algemene fout  | Kan de sleutel niet ophalen.| Dit is een algemene fout. Neem contact op met Microsoft Ondersteuning om de fout op te lossen en de volgende stappen te bepalen.|

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../key-vault/general/overview.md)
- [Quickstart: Een geheim uit Azure Key Vault instellen en ophalen met behulp van de Azure Portal](../key-vault/secrets/quick-create-portal.md)
