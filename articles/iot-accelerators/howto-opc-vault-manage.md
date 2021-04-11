---
title: De OPC-kluis certificaat service beheren-Azure | Microsoft Docs
description: De OPC-kluis basis-CA-certificaten en-gebruikers machtigingen beheren.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0829d4b3fca068ddb0db2df53dd635ab7ad80bed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646161"
---
# <a name="manage-the-opc-vault-certificate-service"></a>De OPC-kluis certificaat service beheren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In dit artikel worden de beheer taken voor de OPC kluis Certificate Management-service in azure beschreven. Het bevat informatie over het vernieuwen van CA-certificaten voor uitgevers, het vernieuwen van de certificaatintrekkingslijst (CRL) en het verlenen en intrekken van gebruikers toegang.

## <a name="create-or-renew-the-root-ca-certificate"></a>Het basis-CA-certificaat maken of vernieuwen

Nadat de OPC-kluis is geïmplementeerd, moet u het basis-CA-certificaat maken. Zonder een geldig CA-certificaat voor de certificaat verlener kunt u geen toepassings certificaten ondertekenen of verlenen. Raadpleeg [certificaten](howto-opc-vault-secure-ca.md#certificates) om uw certificaten te beheren met redelijke, veilige levens duur. Een CA-certificaat van de verlener vernieuwen na de helft van de levens duur. Bij het verlengen moet u er ook voor zorgen dat de geconfigureerde levens duur van een nieuw ondertekend toepassings certificaat niet groter mag zijn dan de levens duur van het CA-certificaat van de verlener.
> [!IMPORTANT]
> De beheerdersrol is vereist om het CA-certificaat van de verlener te maken of te vernieuwen.

1. Open uw certificaat service op `https://myResourceGroup-app.azurewebsites.net` en meld u aan.
2. Ga naar **certificaat groepen**.
3. Er wordt één standaard certificaat groep vermeld. Selecteer **Bewerken**.
4. In **Details van certificaat groep bewerken** kunt u de onderwerpnaam en de levens duur van uw CA-en toepassings certificaten wijzigen. Het onderwerp en de levens duur mogen slechts eenmaal worden ingesteld voordat het eerste CA-certificaat wordt uitgegeven. Wijzigingen in de levens duur tijdens bewerkingen kunnen leiden tot inconsistente levens duur van uitgegeven certificaten en Crl's.
5. Voer een geldig onderwerp in (bijvoorbeeld `CN=My CA Root, O=MyCompany, OU=MyDepartment` ).<br>
   > [!IMPORTANT]
   > Als u het onderwerp wijzigt, moet u het certificaat van de verlener vernieuwen of kunnen toepassings certificaten niet worden ondertekend door de service. Het onderwerp van de configuratie wordt gecontroleerd op basis van het onderwerp van het actieve certificaat van de certificerings instantie. Als de onderwerpen niet overeenkomen, wordt het ondertekenen van certificaten geweigerd.
6. Selecteer **Opslaan**.
7. Als er op dit moment een ' verboden ' fout optreedt, zijn uw gebruikers referenties niet gemachtigd om een nieuw basis certificaat te wijzigen of te maken. De gebruiker die de service heeft geïmplementeerd, heeft standaard de rol Beheerder en ondertekend met de service. Andere gebruikers moeten worden toegevoegd aan de goed keurder, schrijver of beheerder rollen, zoals van toepassing is in de registratie van de Azure Active Directory (Azure AD).
8. Selecteer **Details**. Hiermee wordt de bijgewerkte informatie weer gegeven.
9. Selecteer **CA-certificaat vernieuwen** om het eerste certificaat van de certificerings instantie te verlenen, of om het certificaat van de verlener te vernieuwen. Selecteer vervolgens **OK**.
10. Na een paar seconden worden de **certificaat gegevens** weer geven. Als u het meest recente CA-certificaat en de CRL wilt downloaden voor distributie naar uw OPC UA-toepassingen, selecteert u **verlener** of **CRL**.

De OPC UA Certificate Management-service is nu gereed voor het uitgeven van certificaten voor OPC UA-toepassingen.

## <a name="renew-the-crl"></a>De CRL vernieuwen

Verlenging van de CRL is een update die met regel matige tussen pozen moet worden gedistribueerd naar de toepassingen. OPC UA-apparaten, die ondersteuning bieden voor de x509-extensie van het CRL-distributie punt, kunnen de CRL rechtstreeks bijwerken vanuit het micro service-eind punt. Andere OPC UA-apparaten kunnen hand matig worden bijgewerkt of kunnen worden bijgewerkt met GDS server-push extensies (*) om de vertrouwens lijsten bij te werken met de certificaten en Crl's.

In de volgende werk stroom worden alle certificaat aanvragen in de verwijderde statussen ingetrokken in de Crl's, die overeenkomen met het CA-certificaat van de verlener waarvoor ze zijn uitgegeven. Het versie nummer van de CRL wordt verhoogd met 1. <br>
> [!NOTE]
> Alle uitgegeven Crl's zijn geldig tot de verloopt van het CA-certificaat van de verlener. Dit komt doordat de OPC UA-specificatie geen verplicht, deterministisch distributie model voor CRL vereist.

> [!IMPORTANT]
> De beheerdersrol is vereist voor het vernieuwen van de CRL van de verlener.

1. Open uw certificaat service op `https://myResourceGroup.azurewebsites.net` en meld u aan.
2. Ga naar de pagina **certificaat groepen** .
3. Selecteer **Details**. Hier worden het huidige certificaat en de CRL-informatie weer gegeven.
4. Selecteer **Update CRL-intrekkings lijst (CRL)** om een bijgewerkte CRL uit te geven voor alle actieve certificaat verlener in de OPC-kluis opslag.
5. Na een paar seconden worden de **certificaat gegevens** weer geven. Als u het meest recente CA-certificaat en de CRL wilt downloaden voor distributie naar uw OPC UA-toepassingen, selecteert u **verlener** of **CRL**.

## <a name="manage-user-roles"></a>Gebruikersrollen beheren

U beheert gebruikers rollen voor de OPC-kluis micro service in de zakelijke Azure AD-toepassing. Zie [rollen](howto-opc-vault-secure-ca.md#roles)voor een gedetailleerde beschrijving van de roldefinities.

Standaard kan een geverifieerde gebruiker in de Tenant zich als lezer aanmelden bij de service. Voor rollen met een hogere bevoegdheid moet het Azure Portal hand matig worden beheerd, of met behulp van Power shell.

### <a name="add-user"></a>Gebruiker toevoegen

1. Open Azure Portal.
2. Ga naar **Azure Active Directory**  >  **Enter prise-toepassingen**.
3. De registratie van de OPC-kluis micro service (standaard uw) kiezen `resourceGroupName-service` .
4. Ga naar **gebruikers en groepen**.
5. Selecteer **Add User**.
6. De gebruiker selecteren of uitnodigen voor toewijzing aan een specifieke rol.
7. Selecteer de rol voor de gebruikers.
8. Selecteer **Toewijzen**.
9. Voor gebruikers in de rol beheerder of goed keurder gaat u Azure Key Vault toegangs beleid toevoegen.

### <a name="remove-user"></a>Gebruiker verwijderen

1. Open Azure Portal.
2. Ga naar **Azure Active Directory**  >  **Enter prise-toepassingen**.
3. De registratie van de OPC-kluis micro service (standaard uw) kiezen `resourceGroupName-service` .
4. Ga naar **gebruikers en groepen**.
5. Selecteer een gebruiker met een rol die u wilt verwijderen en selecteer vervolgens **verwijderen**.
6. Voor verwijderde gebruikers in de rol beheerder of goed keurder verwijdert u deze ook uit Azure Key Vault beleid.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Beleid voor gebruikers toegang toevoegen aan Azure Key Vault

Er zijn aanvullende toegangs beleidsregels vereist voor goed keurders en beheerders.

De service-identiteit heeft standaard slechts beperkte machtigingen voor toegang tot Key Vault, om te voor komen dat er verhoogde bewerkingen of wijzigingen worden aangebracht zonder gebruikers imitatie. De basis-service machtigingen zijn Get en List, voor zowel geheimen als certificaten. Voor geheimen is er slechts één uitzonde ring: de service kan een persoonlijke sleutel uit het geheime archief verwijderen nadat deze door een gebruiker is geaccepteerd. Voor alle andere bewerkingen is door de gebruiker geïmiteerde machtigingen vereist.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Voor een rol van goed keurder moeten de volgende machtigingen worden toegevoegd aan Key Vault

1. Open Azure Portal.
2. Ga naar uw OPC `resourceGroupName` -kluis die wordt gebruikt tijdens de implementatie.
3. Ga naar de Key Vault `resourceGroupName-xxxxx` .
4. Ga naar **toegangs beleid**.
5. Selecteer **Nieuw toevoegen**.
6. Sla de sjabloon over. Er is geen sjabloon die voldoet aan de vereisten.
7. Kies **Principal selecteren** en selecteer de gebruiker die u wilt toevoegen, of als u een nieuwe gebruiker wilt uitnodigen voor de Tenant.
8. Selecteer de volgende **belang rijke machtigingen**: **ophalen**, **weer geven** en **ondertekenen**.
9. Selecteer de volgende **geheime machtigingen**: **ophalen**, **weer geven**, **instellen** en **verwijderen**.
10. Selecteer de volgende **machtigingen voor certificaten**: **ophalen** en **weer geven**.
11. Selecteer **OK** en selecteer **Opslaan**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Voor een beheerdersrol moeten de volgende machtigingen worden toegevoegd aan Key Vault

1. Open Azure Portal.
2. Ga naar uw OPC `resourceGroupName` -kluis die wordt gebruikt tijdens de implementatie.
3. Ga naar de Key Vault `resourceGroupName-xxxxx` .
4. Ga naar **toegangs beleid**.
5. Selecteer **Nieuw toevoegen**.
6. Sla de sjabloon over. Er is geen sjabloon die voldoet aan de vereisten.
7. Kies **Principal selecteren** en selecteer de gebruiker die u wilt toevoegen, of als u een nieuwe gebruiker wilt uitnodigen voor de Tenant.
8. Selecteer de volgende **belang rijke machtigingen**: **ophalen**, **weer geven** en **ondertekenen**.
9. Selecteer de volgende **geheime machtigingen**: **ophalen**, **weer geven**, **instellen** en **verwijderen**.
10. Selecteer de volgende **machtigingen voor certificaten**: **ophalen**, **weer geven**, **bijwerken**, **maken** en **importeren**.
11. Selecteer **OK** en selecteer **Opslaan**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Gebruikers toegangs beleid verwijderen uit Azure Key Vault

1. Open Azure Portal.
2. Ga naar uw OPC `resourceGroupName` -kluis die wordt gebruikt tijdens de implementatie.
3. Ga naar de Key Vault `resourceGroupName-xxxxx` .
4. Ga naar **toegangs beleid**.
5. Zoek de gebruiker die u wilt verwijderen en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC-kluis certificaten en-gebruikers beheert, kunt u het volgende doen:

> [!div class="nextstepaction"]
> [Veilige communicatie van OPC-apparaten](howto-opc-vault-secure.md)
