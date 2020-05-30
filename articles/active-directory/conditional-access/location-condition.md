---
title: Podmínka umístění v Azure Active Directory podmíněný přístup
description: Naučte se používat podmínku umístění k řízení přístupu ke cloudovým aplikacím na základě síťového umístění uživatele.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 05/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 781d8b89dd1b7fa6b2ed9707f6d4c485b4abdf20
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2020
ms.locfileid: "84220606"
---
# <a name="using-the-location-condition-in-a-conditional-access-policy"></a>Použití podmínky umístění v zásadách podmíněného přístupu 

Jak je vysvětleno v [článku](overview.md) zásady podmíněného přístupu, jsou na nejvyšší úrovni základní příkazy if-then kombinující signály, jejich rozhodování a prosazování zásad organizace. Jedním z těchto signálů, které je možné začlenit do procesu rozhodování, je umístění v síti.

![Koncepční podmíněný signál plus rozhodnutí pro získání vynucování](./media/location-condition/conditional-access-signal-decision-enforcement.png)

Organizace můžou použít toto síťové umístění pro běžné úkoly, jako je: 

- Vyžadování služby Multi-Factor Authentication pro uživatele, kteří přistupují ke službě, když jsou mimo podnikovou síť.
- Blokování přístupu pro uživatele, kteří přistupují ke službě z určitých zemí nebo oblastí.

Umístění v síti je určené veřejnou IP adresou, kterou klient poskytuje Azure Active Directory. Zásady podmíněného přístupu se ve výchozím nastavení vztahují na všechny adresy IPv4 a IPv6. 

> [!TIP]
> Rozsahy IPV6 se podporují jenom v rozhraní **[pojmenované umístění (Preview)](#preview-features)** . 

## <a name="named-locations"></a>Pojmenovaná umístění

Umístění jsou určena v Azure Portal v části **Azure Active Directory**  >  **zabezpečení**  >  **podmíněného přístupu**  >  **s názvem umístění**. Tato umístění v síti můžou zahrnovat umístění, jako jsou například rozsahy sítě ústředí organizace, rozsahy sítě VPN nebo rozsahy, které chcete blokovat. 

![Pojmenovaná umístění v Azure Portal](./media/location-condition/new-named-location.png)

Pokud chcete nakonfigurovat umístění, budete muset zadat aspoň **název** a rozsah IP adres. 

Počet pojmenovaných umístění, která můžete konfigurovat, se omezuje na velikost souvisejícího objektu ve službě Azure AD. Umístění můžete nakonfigurovat na základě následujících omezení:

- Jedno pojmenované umístění s až 1200 rozsahy IPv4 adres.
- Do každé z nich se přiřadí maximálně 90 pojmenovaných umístění s jedním rozsahem IP adres.

> [!TIP]
> Rozsahy IPV6 se podporují jenom v rozhraní **[pojmenované umístění (Preview)](#preview-features)** . 

### <a name="trusted-locations"></a>Důvěryhodná umístění

Při vytváření síťového umístění má správce možnost označit umístění jako důvěryhodné umístění. 

![Důvěryhodná umístění v Azure Portal](./media/location-condition/new-trusted-location.png)

Tato možnost může být v rámci zásad podmíněného přístupu, kde můžete například vyžadovat registraci služby Multi-Factor Authentication z důvěryhodného umístění v síti. V důsledku toho také vzAzure AD Identity Protection výpočet rizika, což snižuje riziko přihlašování uživatelů, když přichází z místa označeného jako důvěryhodné.

### <a name="countries-and-regions"></a>Země a oblasti

Některé organizace se můžou rozhodnout definovat celé země nebo regiony hranice IP adres jako pojmenovaná umístění pro zásady podmíněného přístupu. Můžou tato umístění používat při blokování zbytečných přenosů, když ví, že platní uživatelé nebudou nikdy přijít z místa, jako je Severní Korea. Tato mapování IP adres na zemi se pravidelně aktualizují. 

> [!NOTE]
> Země neobsahují rozsahy IPv6 adres, jenom známé rozsahy IPv4 adres.

![Vytvořit nové umístění na základě země nebo oblasti v Azure Portal](./media/location-condition/new-named-location-country-region.png)

#### <a name="include-unknown-areas"></a>Zahrnout neznámé oblasti

Některé IP adresy nejsou namapované na konkrétní zemi nebo oblast. Pokud chcete zachytit tato umístění IP adres, zaškrtněte políčko při definování umístění **Zahrnout neznámé oblasti** . Tato možnost umožňuje zvolit, jestli se mají tyto IP adresy zahrnout do pojmenovaného umístění. Toto nastavení použijte, pokud se zásada používající pojmenované umístění má vztahovat na neznámá umístění.

### <a name="configure-mfa-trusted-ips"></a>Konfigurace důvěryhodných IP adres MFA

V [nastavení služby Multi-Factor Authentication Service](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)můžete také nakonfigurovat rozsahy IP adres reprezentující místní intranet vaší organizace. Tato funkce vám umožní nakonfigurovat až 50 rozsahů IP adres. Rozsahy IP adres jsou ve formátu CIDR. Další informace najdete v tématu [důvěryhodné IP adresy](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Pokud máte nakonfigurované důvěryhodné IP adresy, zobrazí se v seznamu umístění pro podmínku umístění jako **důvěryhodné IP adresy MFA** .

### <a name="skipping-multi-factor-authentication"></a>Přeskakuje se Multi-Factor Authentication

Na stránce nastavení služby Multi-Factor Authentication Service můžete identifikovat uživatele firemní sítě intranet tak, že **pro žádosti od federovaných uživatelů v mém intranetu vyberete přeskočit vícefaktorové ověřování**. Toto nastavení indikuje, že deklarace identity uvnitř podnikové sítě, která je vydaná AD FS, by měla být důvěryhodná a slouží k identifikaci uživatele jako v podnikové síti. Další informace najdete v tématu [Povolení funkce důvěryhodných IP adres pomocí podmíněného přístupu](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Po zaškrtnutí této možnosti, včetně pojmenovaného umístění, **důvěryhodných IP adres MFA** budou platit pro všechny zásady s vybranou možností.

U mobilních a desktopových aplikací, které mají dlouhodobé životnosti relací, se podmíněný přístup pravidelně znovu vyhodnocuje. Výchozí hodnota je jednou hodinu. Pokud je deklarace identity uvnitř podnikové sítě vydaná jenom v době počátečního ověřování, služba Azure AD nemusí mít seznam důvěryhodných IP adres. V takovém případě je obtížné zjistit, jestli je uživatel stále v podnikové síti:

1. Ověřte, zda je IP adresa uživatele v jednom z rozsahů důvěryhodných IP adres.
1. Ověřte, zda první tři oktety IP adresy uživatele odpovídají prvním třem oktetům IP adresy počátečního ověřování. IP adresa je porovnávána s počátečním ověřováním, když byla původně vydaná deklarace identity uvnitř podnikové sítě a bylo ověřené umístění uživatele.

Pokud dojde k selhání obou kroků, bude uživatel považován za již nepřipojený k důvěryhodné IP adrese.

## <a name="preview-features"></a>Funkce ve verzi Preview

Kromě všeobecně dostupné funkce pojmenovaného umístění existuje také pojmenované umístění (Preview). K tomuto ukázkovému umístění můžete přistupovat pomocí nápisu v horní části okna aktuálně pojmenovaného umístění.

![Vyzkoušejte si náhled pojmenovaných umístění](./media/location-condition/preview-features.png)

S pojmenovaným umístěním ve verzi Preview je možné

- Nakonfigurovat až 195 pojmenovaných umístění
- Nakonfigurovat až 2000 rozsahů IP adres na pojmenované umístění
- Konfigurace až IPv6 adres

Přidali jsme také několik dalších kontrol, které vám pomůžou snížit změnu chybných konfigurací.

- Rozsahy privátních IP adres už se nedají konfigurovat.
- Počet IP adres, které lze zahrnout do rozsahu, je omezený. Při konfiguraci rozsahu IP adres budou povoleny pouze masky CIDR větší než/8.

Ve verzi Preview jsou teď dvě možnosti vytvoření: 

- **Umístění zemí**
- **Umístění rozsahů IP adres**

> [!NOTE]
> Země neobsahují rozsahy IPv6 adres, jenom známé rozsahy IPv4 adres.

![Rozhraní s pojmenovanými umístěními verze Preview](./media/location-condition/named-location-preview.png)

## <a name="location-condition-in-policy"></a>Podmínka umístění v zásadách

Když konfigurujete podmínku umístění, máte možnost rozlišovat mezi:

- Jakékoli umístění
- Všechna důvěryhodná umístění
- Vybraná umístění

### <a name="any-location"></a>Jakékoli umístění

Ve výchozím nastavení při výběru **kteréhokoli umístění** dojde k použití zásad pro všechny IP adresy, což znamená libovolnou adresu na internetu. Toto nastavení není omezeno na IP adresy, které jste nakonfigurovali jako pojmenované umístění. Když vyberete **libovolné umístění**, můžete i nadále vyloučit konkrétní umístění ze zásad. Můžete například použít zásadu na všechna umístění kromě důvěryhodných umístění a nastavit obor na všechna umístění kromě podnikové sítě.

### <a name="all-trusted-locations"></a>Všechna důvěryhodná umístění

Tato možnost se týká:

- Všechna umístění, která byla označena jako důvěryhodná umístění
- **Důvěryhodné IP adresy MFA** (pokud jsou nakonfigurované)

### <a name="selected-locations"></a>Vybraná umístění

Pomocí této možnosti můžete vybrat jedno nebo více pojmenovaných umístění. Chcete-li použít zásadu s tímto nastavením, musí se uživatel připojit z libovolného umístění. Když kliknete na **Vybrat** ovládací prvek pro výběr sítě, který zobrazuje seznam pojmenovaných sítí, otevře se. V seznamu se zobrazí také informace o tom, zda bylo síťové umístění označeno jako důvěryhodné. Pojmenované umístění s názvem **MFA Trusted IP** adresa slouží k zahrnutí nastavení IP adresy, která se dají konfigurovat na stránce nastavení služby Multi-Factor Authentication Service.

## <a name="what-you-should-know"></a>Co byste měli vědět

### <a name="when-is-a-location-evaluated"></a>Kdy je vyhodnoceno umístění?

Zásady podmíněného přístupu jsou vyhodnocovány v těchto případech:

- Uživatel se zpočátku přihlašuje k webové aplikaci, mobilní aplikaci nebo aplikaci klasické pracovní plochy.
- Mobilní aplikace nebo aplikace klasické pracovní plochy, která používá moderní ověřování, používá k získání nového přístupového tokenu obnovovací token. Ve výchozím nastavení je tato kontrolu jednou za hodinu.

Tato kontrolu znamená, že mobilní a desktopové aplikace využívají moderní ověřování, v průběhu změny síťového umístění se zjistí změna v umístění. Pro mobilní a desktopové aplikace, které nepoužívají moderní ověřování, se zásady použijí u každé žádosti o token. Frekvence požadavku se může lišit v závislosti na aplikaci. Podobně platí, že u webových aplikací se zásady aplikují při počátečním přihlašování a jsou vhodné po dobu života relace ve webové aplikaci. V důsledku rozdílů v životnosti relací napříč aplikacemi se čas mezi vyhodnocením zásad liší také. Pokaždé, když aplikace požádá o nový token přihlášení, zásada se použije.

Ve výchozím nastavení služba Azure AD vydá token po hodinách. Po přesunu podnikové sítě do celé hodiny se zásady vynutily pro aplikace používající moderní ověřování.

### <a name="user-ip-address"></a>IP adresa uživatele

IP adresa, která se používá při vyhodnocování zásad, je veřejná IP adresa uživatele. V případě zařízení v privátní síti tato IP adresa nepředstavuje IP adresu klienta zařízení uživatele v intranetu, jedná se o adresu, kterou síť používá pro připojení k veřejnému Internetu.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Hromadné nahrávání a stahování pojmenovaných umístění

Při vytváření nebo aktualizaci pojmenovaných umístění můžete pro hromadné aktualizace nahrát nebo stáhnout soubor CSV s rozsahy IP adres. Nahrávání nahradí rozsahy IP adres v seznamu těmito rozsahy ze souboru. Každý řádek souboru obsahuje jeden rozsah IP adres ve formátu CIDR.

### <a name="cloud-proxies-and-vpns"></a>Cloudové proxy servery a sítě VPN

Pokud používáte cloudové proxy hostované nebo řešení sítě VPN, IP adresa Azure AD používá při vyhodnocování zásad je IP adresa proxy serveru. Záhlaví X-předané (XFF), které obsahuje veřejnou IP adresu uživatele, se nepoužívá, protože neexistuje žádné ověření, které pochází z důvěryhodného zdroje, takže by mohla představovat metodu pro Faking IP adresu.

Pokud je cloudový proxy server, je možné použít zásadu, která vyžaduje zařízení připojené k doméně, nebo Corpnet deklaraci identity v rámci AD FS.

### <a name="api-support-and-powershell"></a>Podpora rozhraní API a prostředí PowerShell

Rozhraní API a PowerShell ještě nejsou pro pojmenovaná umístění podporovaná.

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak nakonfigurovat zásadu podmíněného přístupu, přečtěte si článek [Vytvoření zásady podmíněného přístupu](concept-conditional-access-policies.md).
- Hledáte ukázkovou zásadu pomocí podmínky umístění? Projděte si článek [podmíněný přístup: blokování přístupu podle umístění](howto-conditional-access-policy-location.md)
