---
title: 'VPN Gateway: Azure AD-Tenant voor P2S VPN-verbindingen: Azure AD-verificatie'
description: U kunt P2S VPN gebruiken om verbinding te maken met uw VNet met behulp van Azure AD-verificatie
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 00db2ed05285a1637414aa1e3adbe3b047ff0568
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641352"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Een Azure Active Directory-Tenant maken voor P2S OpenVPN-protocol verbindingen

Wanneer u verbinding maakt met uw VNet, kunt u verificatie op basis van certificaten of RADIUS-verificatie gebruiken. Wanneer u echter het open VPN-protocol gebruikt, kunt u ook Azure Active Directory-verificatie gebruiken. Dit artikel helpt u bij het instellen van een Azure AD-Tenant voor P2S open VPN-verificatie.

> [!NOTE]
> Azure AD-verificatie wordt alleen ondersteund voor OpenVPN®-protocol verbindingen.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Azure AD-Tenant verifiëren

Controleer of u een Azure AD-Tenant hebt. Als u geen Azure AD-Tenant hebt, kunt u er een maken met behulp van de stappen in het artikel [een nieuwe Tenant maken](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisatie naam
* Oorspronkelijke domeinnaam

Voorbeeld:

   ![Nieuwe Azure AD-Tenant](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. gebruikers van Azure AD-tenants maken

Uw Azure AD-Tenant heeft de volgende accounts nodig: een globaal beheerders account en een hoofd gebruikers account. Het hoofd gebruikers account wordt gebruikt als uw hoofd account voor insluiten (Service-account). Wanneer u een Azure AD-Tenant gebruikers account maakt, past u de Directory-rol aan voor het type gebruiker dat u wilt maken.

Volg de stappen in [dit artikel](../active-directory/fundamentals/add-users-azure-active-directory.md) om ten minste twee gebruikers te maken voor uw Azure AD-Tenant. Zorg ervoor dat u de **Directory-rol** wijzigt om de volgende account typen te maken:

* Globale beheerder
* Gebruiker

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Azure AD-verificatie inschakelen op de VPN-gateway

1. Zoek de Directory-ID van de map die u voor verificatie wilt gebruiken. Deze wordt weer gegeven in de sectie eigenschappen van de pagina Active Directory.

    ![Map-ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Kopieer de Map-id.

3. Meld u aan bij de Azure Portal als gebruiker aan wie de rol van **globale beheerder** is toegewezen.

4. Geef vervolgens toestemming voor de beheerder. Kopieer en plak de URL die betrekking heeft op uw implementatie locatie in de adres balk van uw browser:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Duitsland

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Selecteer het account van de **globale beheerder** als u hierom wordt gevraagd.

    ![Map-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Selecteer **accepteren** wanneer u hierom wordt gevraagd.

    ![Accepteren](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Onder uw Azure AD, in **bedrijfs toepassingen**, wordt **Azure VPN** weer gegeven.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Als u nog geen werkende Point-to-site-omgeving hebt, volgt u de instructie om er een te maken. Zie [een punt-naar-site-VPN maken](vpn-gateway-howto-point-to-site-resource-manager-portal.md) voor het maken en configureren van een punt-naar-site-VPN-gateway met systeem eigen Azure-certificaat verificatie. 

    > [!IMPORTANT]
    > De basis-SKU wordt niet ondersteund voor OpenVPN.

9. Schakel Azure AD-verificatie in op de VPN-gateway door de volgende opdrachten uit te voeren. Zorg er daarom voor dat u de opdracht wijzigt in overeenstemming met uw eigen omgeving:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Zorg ervoor dat u een afsluitende slash aan het einde van `AadIssuerUri` de waarde opneemt. Anders mislukt de opdracht.

10. Maak en down load het profiel door de volgende opdrachten uit te voeren. Wijzig de waarden voor-ResourceGroupName en-name zodat deze overeenkomen met uw eigen waarde.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Na het uitvoeren van de opdrachten ziet u een resultaat zoals hieronder. Kopieer de URL van het resultaat naar uw browser om het profiel-zip-bestand te downloaden.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Pak het gedownloade zip-bestand uit.

13. Blader naar de map ungezipte ' AzureVPN '.

14. Noteer de locatie van het bestand azurevpnconfig. XML. Azurevpnconfig. XML bevat de instelling voor de VPN-verbinding en kan rechtstreeks in de Azure VPN-client toepassing worden geïmporteerd. U kunt dit bestand ook distribueren naar alle gebruikers die verbinding moeten maken via e-mail of een andere manier. De gebruiker heeft geldige Azure AD-referenties nodig om verbinding te kunnen maken.

## <a name="next-steps"></a>Volgende stappen

Als u verbinding wilt maken met uw virtuele netwerk, moet u een VPN-client profiel maken en configureren. Zie [Configure a VPN client for P2S VPN connections](openvpn-azure-ad-client.md).
