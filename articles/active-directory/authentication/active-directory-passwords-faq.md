---
title: Samoobslužné resetování hesla – nejčastější dotazy – Azure Active Directory
description: Nejčastější dotazy ohledně služby Azure AD hesla pomocí samoobslužné služby obnovit
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: f98b72d9b5e59155bb7215181fd1ecd336126ee6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440972"
---
# <a name="password-management-frequently-asked-questions"></a>Správa hesel – nejčastější dotazy

Tady jsou některé nejčastější dotazy (FAQ) pro všechny věci týkající se resetování hesla.

Pokud máte obecný dotaz ohledně služby Azure Active Directory (Azure AD) a samoobslužné resetování hesla (SSPR), který jsem zde nenalezl odpověď, můžete požádat o komunity pomoc na [fórum služby Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Členy komunity patří technici, produktové manažery, MVP a ostatními Odborníci v oblasti IT.

Tyto nejčastější dotazy je rozdělený do následujících částí:

* [Registrace pro resetování dotazy týkající se heslo](#password-reset-registration)
* [Otázky ohledně resetování hesel](#password-reset)
* [Dotazy týkající se změna hesla](#password-change)
* [Dotazy týkající se sestav správy hesel](#password-management-reports)
* [Dotazy týkající se zpětným zápisem hesla](#password-writeback)

## <a name="password-reset-registration"></a>Registrace resetování hesla

* **Otázka:  Můžete svým uživatelům registrovat svá vlastní data pro resetování hesla?**

  > **Odpověď:** Ano. Resetování hesla je povoleno a pokud jsou licencované, uživatel může přejít na portál pro registraci resetování hesel (https://aka.ms/ssprsetup) k registraci jejich ověřovacích informací. Uživatelé můžou taky zaregistrovat přes přístupový Panel (https://myapps.microsoft.com). Registrace přes přístupový Panel, vyberte jejich profilový obrázek, vyberte potřebují **profilu**a pak vyberte **zaregistrovat pro resetování hesla** možnost.
  >
  >
* **Otázka:  Je-li povolit heslo resetoval skupinu a potom se rozhodnete povolit všem uživatelům se Moje uživatelů vyžaduje znovu zaregistrovat?**

  > **Odpověď:** Ne. Uživatelé, kteří jste vyplnili ověřovacích dat není nutné znovu zaregistrovat.
  >
  >
* **Otázka:  Můžete definovat data resetování hesel jménem uživatelů?**

  > **Odpověď:** Ano, můžete tak učinit pomocí Azure AD Connect, PowerShell, [webu Azure portal](https://portal.azure.com), nebo [centra pro správu služeb Microsoft 365](https://admin.microsoft.com). Další informace najdete v tématu [obnovit Data používaná hesla pomocí samoobslužné služby Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka:  Můžete synchronizovat data k zodpovězení bezpečnostních otázek v místním?**

  > **Odpověď:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka:  Můžete svým uživatelům zaregistrovat data tak, že ostatní uživatelé neuvidí tato data?**

  > **Odpověď:** Ano. Když uživatelé registrují dat s použitím hesla resetovat portál pro registraci, jsou data uložena do privátní ověřování polí, která jsou viditelné pouze pro globální správce a uživatele.
  >
  >
* **Otázka:  Mají uživatelé k registraci, aby mohli používat resetování hesla?**

  > **Odpověď:** Ne. Pokud definujete dostatek informací o ověřování za ně, uživatelé nemají k registraci. Funguje resetování hesel tak dlouho, dokud máte správně naformátovaná data uložená v příslušných polí v adresáři.
  >
  >
* **Otázka:  Můžete synchronizovat nebo nastavit pole Telefon pro ověření, ověřování e-mailu nebo alternativní ověření telefonu jménem uživatelů?**

  > **Odpověď:** Pole, které mohou být nastaven globální správce, jsou definovány v následujícím článku [požadavky SSPR na Data](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka:  Jak na portál pro registraci Určuje, jaké možnosti zobrazíte mých uživatelů?**

  > **Odpověď:** Registrační portál pro resetování hesla zobrazí pouze možnosti, že jste povolili pro vaše uživatele. Tyto možnosti se nacházejí v rámci **zásady resetování hesel uživateli** svého adresáře **konfigurovat** kartu. Například pokud nepovolíte bezpečnostní otázky, pak uživatelé nejsou možné zaregistrovat pro tuto možnost.
  >
  >
* **Otázka:  Když se považuje za uživatel zaregistrován?**

  > **Odpověď:** Uživatel se považuje za zaregistrovali samoobslužné resetování HESLA, když jste zaregistrovali alespoň **počet metod požadovaných k resetování** heslo, které jste nastavili v [webu Azure portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetování hesla

* **Otázka:  Můžete zabránit uživatelům ve vícenásobné pokusy o resetování hesla v krátké době?**

  > **Odpověď:** Ano, jsou funkce zabezpečení, které jsou součástí pro ochranu před zneužitím resetování hesla. 
  >
  > Uživatelům můžete zkusit pouze pět resetování pokusů o zadání hesla v rámci období 24 hodin, než jste uzamčen po dobu 24 hodin. 
  >
  > Uživatelé můžou pokusí ověřit telefonní číslo poslat zprávu SMS a ověřit bezpečnostní otázky a odpovědi pouze pětkrát za hodinu, než jste uzamčen po dobu 24 hodin. 
  >
  > Uživatelé můžou posílat e-mailu maximálně 10 pokusech během 10 minut doby před jejich uzamčení po dobu 24 hodin.
  >
  > Čítače se vynuluje, jakmile uživatel může resetovat své heslo.
  >
  >
* **Otázka:  Jak dlouho musí čekat na dostávat e-mailu, SMS nebo telefonní hovor z resetování hesla?**

  > **Odpověď:** E-mailů, zpráv SMS a telefonních hovorů by měl doručení necelou minutu. Normální případ je 5 až 20 sekund.
  > Pokud jste neobdrželi oznámení v tomto časovém rámci:
  > * Zkontrolujte složku s nevyžádanou poštou.
  > * Zkontrolujte, že číslo nebo e-mailu kontaktovaný ten, který jste očekávali.
  > * Zkontrolujte, jestli je správně ověřovací data v adresáři ve formátu, například + 1 4255551234 nebo *uživatele\@contoso.com*. 
* **Otázka:  Jaké jazyky jsou podporovány resetování hesla?**

  > **Odpověď:** Resetovat heslo uživatelského rozhraní, zpráv SMS a hlasových hovorů byly lokalizovány do stejné jazyky, které jsou podporovány v Office 365.
  >
  >
* **Otázka:  Které části prostředí pro resetování hesla získat značky, když jsem nastavil organizační značky položky v mém adresáři je karta Konfigurace?**

  > **Odpověď:** Portál pro resetování hesla zobrazuje logo vaší organizace a umožňuje vám nakonfigurovat odkaz "Kontaktujte správce" tak, aby odkazoval na vlastní e-mailu nebo adresa URL. Všechny e-maily, které odesílají resetování hesla zahrnuje logo vaší organizace, barvy a název v textu e-mailu a je přizpůsobit z nastavení pro tento konkrétní název.
  >
  >
* **Otázka:  Jak lze informovat Moji uživatelé o tom, kde přejdete k resetování hesel?**

  > **Odpověď:** Vyzkoušejte některou z návrhů v našich [nasazení samoobslužného resetování HESLA](howto-sspr-deployment.md#communications-plan) článku.
  >
  >
* **Otázka:  Můžete použít tuto stránku na mobilním zařízení?**

  > **Odpověď:** Ano, tato stránka pracuje na mobilních zařízeních.
  >
  >
* **Otázka:  Podporujete odemykání místní účty Active Directory, když uživatelům resetovat svá hesla?**

  > **Odpověď:** Ano. Pokud uživatel obnoví heslo, pokud byla nasazena zpětný zápis hesla prostřednictvím Azure AD Connect, tento uživatelský účet je automaticky odemknout při jejich resetování hesla.
  >
  >
* **Otázka:  Jak můžu integrovat přímo do daného uživatele klasické pracovní plochy přihlašování resetování hesla?**

  > **Odpověď:** Pokud jste zákazníkem Azure AD Premium, můžete nainstalovat Microsoft Identity Manageru bez dalších poplatků a nasadit resetování hesla místního řešení.
  >
  >
* **Otázka:  Můžete nastavit různé bezpečnostní otázky pro různá národní prostředí?**

  > **Odpověď:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka:  Kolik otázky můžete nakonfigurovat pro možnost ověřování otázky zabezpečení?**

  > **Odpověď:** Můžete nakonfigurovat až 20 vlastní bezpečnostní otázky v [webu Azure portal](https://portal.azure.com).
  >
  >
* **Otázka:  Jak dlouho smí bezpečnostní otázky být?**

  > **Odpověď:** Bezpečnostní otázky mohou být dlouhé 3 až 200 znaků.
  >
  >
* **Otázka:  Jak dlouho smí odpovědi na bezpečnostní otázky být?**

  > **Odpověď:** Odpovědi mohou být dlouhé 3 až 40 znaků.
  >
  >
* **Otázka:  Jsou duplicitní odpovědi na bezpečnostní otázky odmítl?**

  > **Odpověď:** Ano, můžeme odmítnout duplicitní odpovědi na bezpečnostní otázky.
  >
  >
* **Otázka:  Může uživatel zaregistrovat na stejné bezpečnostní otázku více než jednou?**

  > **Odpověď:** Ne. Poté, co uživatel zaregistruje na určitou otázku, nelze zaregistrovat tento dotaz znovu.
  >
  >
* **Otázka:  Je možné nastavit minimální limit bezpečnostní otázky pro registraci a resetování?**

  > **Odpověď:** Ano, jednoho limitu lze nastavit za registrační poplatky a druhý pro obnovení. Tři až pět otázek zabezpečení mohou být požadovány pro registraci, může být a tři až pět otázek požadovaných k resetování.
  >
  >
* **Otázka:  Jsem nakonfiguroval Moje zásady budou muset uživatelé použít bezpečnostní otázky pro obnovení, ale Azure správci zdá se, že se konfigurují jinak.**

  > **Odpověď:** Toto je očekávané chování. Microsoft pro všechny role správců Azure vynucuje přísné výchozí zásady resetování hesel. To zabrání tomu, aby správce pomocí bezpečnostních otázek. Další informace o těchto zásadách v [zásady hesel a omezení v Azure Active Directory](concept-sspr-policy.md) článku.
  >
  >
* **Otázka:  Pokud uživatel zaregistroval více než maximální počet otázek požadovaných k resetování, jak se na otázky zabezpečení, které vybírají během obnovení?**

  > **Odpověď:** *N* počet bezpečnostních otázek náhodně vybrány z celkového počtu otázek uživatel zaregistroval pro, kde *N* , který je nastaven pro velikost **počet otázek požadovaných k resetování** možnost. Například pokud uživatel zaregistroval pět bezpečnostní otázky, ale pouze tři nutných k resetování hesla, tři pět otázek náhodně vybrány a jsou uvedeny v resetování. Aby se zabránilo otázku bombarduje, pokud uživatel získá odpovědi na otázky nesprávné procesu výběru se začne přehrávat znovu.
  >
  >
* **Otázka:  Jak dlouho jsou e-mailu a SMS jednorázového hesla platná?**

  > **Odpověď:** Životnost relace pro resetování hesla je 15 minut. Od samého začátku operaci resetování hesla uživatele má 15 minut k resetování hesla. Po vypršení platnosti tohoto časového období nejsou platné e-mailu a SMS jednorázovým heslem.
  >
  >
* **Otázka:  Můžete zablokuje uživatelům možnost resetování hesla?**

  > **Odpověď:** Ano, pokud používáte skupiny Pokud chcete povolit samoobslužné resetování HESLA, je odebrání jednotlivého uživatele ze skupiny, které umožňuje uživatelům resetovat své heslo. Pokud uživatel je globálním správcem zachovávají možnost k resetování hesla a to nejde zakázat.
  >
  >

## <a name="password-change"></a>Změna hesla

* **Otázka:  Kde by měli uživatelé přejít ke změně hesla?**

  > **Odpověď:** Uživatelé mohou změnit svá hesla kdekoli uvidí jejich profilový obrázek nebo ikonu, jako je v pravém horním rohu jejich [Office 365](https://portal.office.com) portál nebo [přístupového panelu](https://myapps.microsoft.com) prostředí. Uživatelé mohou změnit heslo, [stránka profil přístupového panelu](https://account.activedirectory.windowsazure.com/r#/profile). Uživatelé mohou také výzva ke změně hesla na přihlašovací stránce služby Azure AD automaticky, pokud vypršela platnost hesla. A konečně, můžou uživatelé procházet k [portál pro změnu hesel Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) přímo Pokud chtějí ke změně hesla.
  >
  >
* **Otázka:  Můžete se Moji uživatelé upozornění na portálu Office, když vyprší platnost hesla v místním?**

  > **Odpověď:** Ano, to je možné ještě dnes používáte Active Directory Federation Services (AD FS). Pokud používáte službu AD FS, postupujte podle pokynů [odesílání deklarací identity zásady hesel se službou AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) článku. Pokud používáte synchronizaci hodnot hash hesel, to není možné ještě dnes. Jsme nesynchronizují zásady hesel z místních adresářů, takže není možné, abychom mohli posílat oznámení o vypršení platnosti na cloudové prostředí. V obou případech je také možné [upozornit uživatele, jejichž hesla se vyprší prostřednictvím prostředí PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **Otázka:  Můžete zablokovat uživatelům možnost měnit své heslo?**

  > **Odpověď:** Pro uživatele jenom pro cloud nejde blokovat změny hesla. Pro místní uživatele, můžete nastavit **uživatel nemůže změnit heslo** možnost vybrat. Vybraní uživatelé nelze změnit své heslo.
  >
  >

## <a name="password-management-reports"></a>Sestav správy hesel

* **Otázka:  Jak dlouho trvá data zobrazit na sestav správy hesel?**

  > **Odpověď:** Data by se zobrazit na sestav správy hesel v 5 až 10 minut. V některých případech to může trvat až hodinu, se zobrazí.
  >
  >
* **Otázka:  Jak mohu filtrovat sestav správy hesel**

  > **Odpověď:** Pro filtrování sestav správy hesel, vyberte na malé lupu extreme napravo od popisků sloupců v horní části sestavy. Pokud chcete udělat rozsáhlejší filtrování, můžete stáhnout sestavu aplikace Excel a vytvořte kontingenční tabulky.
  >
  >
* **Otázka: Jaký je maximální počet událostí, které jsou uloženy v sestav správy hesel?**

  > **Odpověď:** Až 75 000 resetování hesel nebo hesel událostí registrace resetování ukládají do sestav správy hesel pokrývání uzlů až zpět na hodnotu 30 dnů. Pracujeme na tom rozbalte toto číslo zahrnout další události.
  >
  >
* **Otázka:  Jak daleko sestav správy hesel obrátit?**

  > **Odpověď:** Zobrazení sestav správy hesel operace, ke kterým došlo v posledních 30 dní. Teď Pokud budete potřebovat k archivaci dat, můžete pravidelně stažení sestavy a uložit v samostatném umístění.
  >
  >
* **Otázka:  Existuje maximální počet řádků, které se mohou objevit na sestav správy hesel?**

  > **Odpověď:** Ano. Nesmí být delší než 75 000 řádků se může objevit v jedné ze sestav správy hesel, zda jsou zobrazeny v uživatelském rozhraní nebo jsou staženy.
  >
  >
* **Otázka:  Je k dispozici rozhraní API pro přístup k resetování hesla nebo registrační data pro generování sestav?**

  > **Odpověď:** Ano. Informace o tom, jak lze získat přístup k resetování hesla, vytváření sestav datového proudu, naleznete v tématu [zjistěte, jak získat přístup k resetování hesla události vytváření sestav prostřednictvím kódu programu](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Zpětný zápis hesla

* **Otázka:  Jak funguje zpětný zápis hesla na pozadí?**

  > **Odpověď:** Přečtěte si článek [fungování zpětného zápisu hesla](howto-sspr-writeback.md) pro vysvětlení, co se stane, když povolíte zpětný zápis hesla a jak data protékají systému zpět do místního prostředí.
  >
  >
* **Otázka:  Jak dlouho zpětný zápis hesla trvá práci? Je zde synchronizaci zpoždění stejně, jako je se synchronizací hodnot hash hesel?**

  > **Odpověď:** Zpětný zápis hesla je rychlé. Je synchronní kanál, který funguje zásadně jiným způsobem než synchronizaci hodnot hash hesel. Zpětný zápis hesla umožňuje uživatelům získat v reálném čase zpětnou vazbu o úspěšném resetování hesla nebo operaci změny. Průměrná doba pro úspěšné zpětný zápis hesla je pod 500 ms.
  >
  >
* **Otázka:  Pokud Můj místní účet je zakázaný, jak se Moje cloudový účet a vliv na přístup?**

  > **Odpověď:** Pokud vaše místní ID je zakázaná, ID cloudu a přístup se také deaktivuje na další interval synchronizace prostřednictvím služby Azure AD Connect. Ve výchozím nastavení je tato synchronizace každých 30 minut.
  >
  >
* **Otázka:  Pokud Můj účet v místním je omezen pomocí zásad hesel místní služby Active Directory, samoobslužné resetování HESLA dodržují tato zásada po změně hesla?**

  > **Odpověď:** Ano, samoobslužné resetování HESLA využívá a dodržuje zásady hesel místní služby Active Directory. Tyto zásady obsahují Typická zásad hesla v doméně služby Active Directory, stejně jako všechny heslo definované, podrobné zásady, které jsou zaměřeny na uživatele.
  >
  >
* **Otázka:  Jaké typy zpětného zápisu hesla účtů fungovat?**

  > **Odpověď:** Zpětný zápis hesla funguje pro uživatelské účty synchronizované z místní služby Active Directory do služby Azure AD, včetně federované, synchronizované a výkonem průchozích Autentication uživatelé hash hesla.
  >
  >
* **Otázka:  Zpětný zápis hesla vynutit zásady hesel Moje doména?**

  > **Odpověď:** Ano. Zpětný zápis hesla vynucuje stáří hesla, historie, složitost, filtry a další omezení, které můžete ukládat na místě na hesla v místní doméně.
  >
  >
* **Otázka:  Zpětný zápis hesla je zabezpečené?  Jak se může být jisti, že mám nebudete získáte hacker?**

  > **Odpověď:** Ano, jsou zabezpečené zpětný zápis hesla. Přečtěte si další informace o několik vrstev zabezpečení implementovaných službou zpětný zápis hesla, přečtěte si [zabezpečení zpětného zápisu hesla](concept-sspr-writeback.md#password-writeback-security) tématu [přehled zpětný zápis hesla](howto-sspr-writeback.md) článku.
  >
  >

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
