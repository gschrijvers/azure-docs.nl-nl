---
title: De gegevens stroom van het wacht woord voor de resource-eigenaar configureren
titleSuffix: Azure AD B2C
description: Meer informatie over het configureren van de ROPC-stroom in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d1989f65f73ac4f9dc8dd328fa9d7ed267eec1aa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83636415"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>De gegevens stroom van het wacht woord voor de resource-eigenaar configureren in Azure AD B2C

De ROPC-stroom (resource owner password credentials) is een OAuth-standaard verificatie stroom waarbij de toepassing, ook bekend als de Relying Party, geldige referenties, zoals gebruikers naam en wacht woord, uitwisselt voor een ID-token, toegangs token en een vernieuwings token.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

[!INCLUDE [active-directory-b2c-ropc-notes](../../includes/active-directory-b2c-ropc-notes.md)]

##  <a name="create-a-resource-owner-user-flow"></a>Een gebruikers stroom van een resource-eigenaar maken

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij Azure Portal.
2. Als u wilt overschakelen naar uw Azure AD B2C Tenant, selecteert u de map B2C in de rechter bovenhoek van de portal.
3. Klik op **gebruikers stromen**en selecteer **nieuwe gebruikers stroom**.
4. Klik op het tabblad **alle** en selecteer **Aanmelden met behulp van ROPC**.
5. Geef een naam op voor de gebruikers stroom, bijvoorbeeld *ROPC_Auth*.
6. Klik onder **toepassings claims**op **weer geven**.
7. Selecteer de toepassings claims die u nodig hebt voor uw toepassing, zoals weergave naam, e-mail adres en ID-provider.
8. Selecteer **OK**, en selecteer vervolgens **Maken**.
9. Klik op **gebruikers stroom uitvoeren**.

   Vervolgens ziet u een eind punt zoals dit voor beeld:

   `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_ROPC_Auth/v2.0/.well-known/openid-configuration`


## <a name="register-an-application"></a>Een toepassing registreren

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

## <a name="test-the-user-flow"></a>De gebruikers stroom testen

Gebruik uw favoriete API-ontwikkelings toepassing om een API-aanroep te genereren en Bekijk het antwoord op fout opsporing van uw gebruikers stroom. Maak een aanroep als dit met de informatie in de volgende tabel als hoofd tekst van de POST-aanvraag:
- Vervang * \< tenant naam>. onmicrosoft.com* door de naam van uw B2C-Tenant.
- Vervang * \< B2C_1A_ROPC_Auth>* door de volledige naam van het beleid voor wachtwoord referenties van uw resource-eigenaar.
- Vervang * \< bef2222d56-552f-4a5b-b90a-1988a7d634c3>* door de toepassings-id van uw registratie.

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_ROPC_Auth/oauth2/v2.0/token`

| Sleutel | Waarde |
| --- | ----- |
| gebruikersnaam | leadiocl@outlook.com |
| wachtwoord | Passxword1 |
| grant_type | wachtwoord |
| scope | OpenID Connect \< bef2222d56-552f-4a5b-b90a-1988a7d634c3> offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| response_type | token id_token |

*Client_id* is de waarde die u eerder hebt genoteerd als de toepassings-id. *Offline_access* is optioneel als u een vernieuwings token wilt ontvangen. De gebruikers naam en het wacht woord die u gebruikt, moeten referenties zijn van een bestaande gebruiker in uw Azure AD B2C-Tenant.

De werkelijke POST-aanvraag ziet er als volgt uit:

```
POST /<tenant-name>.onmicrosoft.com/B2C_1_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Een geslaagde reactie met offline toegang lijkt op het volgende voor beeld:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg"
}
```

## <a name="redeem-a-refresh-token"></a>Een vernieuwings token inwisselen

Een POST-aanroep maken zoals deze wordt weer gegeven met de informatie in de volgende tabel als hoofd tekst van de aanvraag:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_ROPC_Auth/oauth2/v2.0/token`

| Sleutel | Waarde |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| resource | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3> |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* en *resource* zijn de waarden die u eerder hebt genoteerd als de toepassings-id. *Refresh_token* is het token dat u in de eerder genoemde authenticatie oproep hebt ontvangen.

Een geslaagde reactie ziet eruit als in het volgende voor beeld:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhTmsifQ.eyJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNTE2ZmMwNjUtZmYzNi00YjkzLWFhNWEtZDZlZWRhN2NiYWM4L3YyLjAvIiwiZXhwIjoxNTMzNjc2NTkwLCJuYmYiOjE1MzM2NzI5OTAsImF1ZCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsImlkcCI6IkxvY2FsQWNjb3VudCIsInN1YiI6ImJmZDgwODBjLTBjNDAtNDNjYS05ZTI3LTUyZTAyNzIyNWYyMSIsIm5hbWUiOiJEYXZpZE11IiwiZW1haWxzIjpbImRhdmlkd20xMDMwQGhvdG1haWwuY29tIl0sInRmcCI6IkIyQ18xX1JPUENfQXV0aCIsImF6cCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsInZlciI6IjEuMCIsImlhdCI6MTUzMzY3Mjk5MH0.RULWeBR8--s5cCGG6XOi8m-AGyCaASx9W5B3tNUQjbVkHnGdo2_OUrnVoOZ1PTcrc1b0PQM2kVWi7NpYn57ifnqL_feTJPDbj9FJ8BmyxULdoECWxSM6KHsOPWZOIg5y1lNwN_IQ2HNF6UaDyYf1ZIM-jHr-uSfUnQXyWRnGDwNKX7TQbFmFk4oFMbPxTE7ioWAmxSnroiiB4__P9D0rUM1vf_qfzemf2ErIWSF9rGtCNBG-BvJlr3ZMCxIhRiIWNM2bVY0i3Nprzj0V8_FM6q8U19bvg9yDEzUcbe_1PMqzP3IrXW9N1XvQHupsOj8Keb7SmpgY1GG091X6wBCypw",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhTmsifQ.eyJleHAiOjE1MzM2NzY1OTAsIm5iZiI6MTUzMzY3Mjk5MCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzUxNmZjMDY1LWZmMzYtNGI5My1hYTVhLWQ2ZWVkYTdjYmFjOC92Mi4wLyIsInN1YiI6ImJmZDgwODBjLTBjNDAtNDNjYS05ZTI3LTUyZTAyNzIyNWYyMSIsImF1ZCI6IjljNTA2MThjLWY5NTEtNDlhNS1iZmU1LWQ3ODA4NTEyMWMzYSIsImlhdCI6MTUzMzY3Mjk5MCwiYXV0aF90aW1lIjoxNTMzNjcyOTkwLCJpZHAiOiJMb2NhbEFjY291bnQiLCJuYW1lIjoiRGF2aWRNdSIsImVtYWlscyI6WyJkYXZpZHdtMTAzMEBob3RtYWlsLmNvbSJdLCJ0ZnAiOiJCMkNfMV9ST1BDX0F1dGgiLCJhdF9oYXNoIjoiYW5hZ3QtX1NveUtBQV9UNFBLaHN4dyJ9.bPzpUFh94XFHXC_yR6qH_Unf6_hN-9-BjDXOzrdb1AuoU6-owQ3fWDxNBUbYEPALid3sgm4qhJ6BROFKryD8aWfrNyaErnYZwZ6rliHk4foa3JsbDgM3yNGPL0hzOFpC4Y9QhUjNgQOxvnQLtqbHVNonSvBc7VVPAjBDza44GowmvLORfJ1qkTjdrFM75HlLVeQch8cUNf-Ova77JdG5WHgYgqRhAq1OhV68YgEpQkARyz77zbAz9zZEHZZlgsli8UV6C-CPcmoHbwS-85mLzF9nLxhzjgIXJwckB6I7lvTpfuRtaqZIb3pMYeHZJaxaNLDvq9Qe4N-danXABg1B2w",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAifQ..8oC4Q6aKdr35yMWm.p43lns-cfWNFbtmrhvtssQXCItb3E9aSLafZJ6nKnnpXGQ-ZapOOyH7hPK7AN_RT7NMsQwNdy0Fyv_hOMrFbMPZNvHSa91RsQIvBZ73-CVy0HNF0grSezjCATg4NVHfricuQVegEmZKFOoNP6TaMC2kIlEi3rhrrO8VE3ZFQ3Jjo6j91BJaE9ybb02HWOoKqlzHiazwQyUHujw_R0TyXaQCI_gtLARr5QUXm7hlAfHhxR9uewQKlRbeuMH8nCMLSMASCJyzfeSJTjXmA0F0VrXozrqzOJdyy0EETPR7oA48MJ9l6C2sy2ZELkqpOM3xhbhV-Re7nM09b8DeWuCw7VNTcQc9DKnIHDR-H5U2Tc-lMJQadgUNZv7KGSRGTyprWb7wF7FEPnRNID5PCDV_N_yoQpI7VvJO_NotXEgHFo7OHs5Gsgwpl5mrDtymYzIMM7onTflOlu46em_qltji7xcWNOuHq4AeOlcY9ZythZgJH7livljReTwyX8QuUwpomXVEUGDc5pAnvgSozxnUbM7AlwfUeJZRT45P7L7683RSqChdNxiQk0sXUECqxnFxMAz4VUzld2yFe-pzvxFF4_feQjBEmSCAvekpvJUrEticEs4QzByV5UZ2ZCKccijFTg4doACiCo_z13JTm47mxm-5jUhXOQqiL69oxztk.KqI-z2LlC77lvwqmeFtdGQ",
    "refresh_token_expires_in": 1209600
}
```
> [!NOTE]
> Bij het maken van gebruikers via Graph API moet de toepassing de machtigingen ' OpenID Connect ', ' offline_access ' en ' profile ' van Microsoft Graph hebben.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementeer met uw voorkeurs systeem eigen SDK of gebruik app-auth

De implementatie van de Azure AD B2C voldoet aan de OAuth 2,0-standaarden voor de referenties van het eigenaars wachtwoord voor open bare client bronnen en moet compatibel zijn met de meeste client-Sdk's. We hebben deze stroom uitgebreid getest in productie, met AppAuth voor iOS en AppAuth voor Android. Zie voor de meest recente informatie [systeem eigen app SDK voor OAuth 2,0 en OpenID Connect Connect implementeren moderne Best practices](https://appauth.io/).

Down load Working-voor beelden die zijn geconfigureerd voor gebruik met Azure AD B2C van GitHub, [voor Android](https://aka.ms/aadb2cappauthropc) en [IOS](https://aka.ms/aadb2ciosappauthropc).




