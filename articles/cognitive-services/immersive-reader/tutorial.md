---
title: 'Kurz: Spuštění moderního čtečky pomocí Node. js'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci Node. js, která spustí moderní čtečku.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 73f9ee597682cc995f3a2cc783abeee92bf11bd2
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501147"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Kurz: Spuštění Asistivní čtečky (Node.js)

V tomto [přehledu](./overview.md)jste se dozvěděli o tom, co je moderní čtečka a jak implementuje osvědčené techniky pro zlepšení porozumění čtení pro jazykové učení, vznikající čtenáři a studenty s rozdíly v učení. V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci Node. js, která spustí moderní čtečku. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace v Node. js pomocí Expressu
> * Získání přístupového tokenu
> * Spuštění moderního čtecího zařízení s ukázkovým obsahem
> * Zadejte jazyk vašeho obsahu
> * Zadejte jazyk rozhraní moderního čtecího zařízení.
> * Spuštění moderního čtečky s využitím matematického obsahu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory (Azure AD). Pomocí [těchto pokynů](./azure-active-directory-authentication.md) si můžete nastavit. Při konfiguraci vlastností prostředí budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [Node. js](https://nodejs.org/) a [příze](https://yarnpkg.com)
* Rozhraní IDE, jako je například [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Vytvoření webové aplikace v Node. js pomocí Expressu

Pomocí `express-generator` nástroje vytvořte webovou aplikaci v Node. js.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Nainstalujte závislosti příze a přidejte závislosti `request` a `dotenv`, které se použijí později v tomto kurzu.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Získání ověřovacího tokenu Azure AD

Potom napíšete back-end rozhraní API pro načtení ověřovacího tokenu Azure AD.

Pro tuto část potřebujete od výše uvedeného kroku požadavků konfigurace ověřování Azure AD nějaké hodnoty. Vraťte se zpět k textovému souboru, který jste si uložili do této relace.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Jakmile máte tyto hodnoty, vytvořte nový soubor s názvem _. env_a vložte do něj následující kód a zadejte hodnoty vlastních vlastností z výše uvedeného.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Ujistěte se, že tento soubor nechcete potvrdit do správy zdrojových kódů, protože obsahuje tajné klíče, které by neměly být zveřejněny.

V dalším kroku otevřete _App. js_ a na začátek souboru přidejte následující. Tím se načte vlastnosti definované v souboru. env jako proměnné prostředí do uzlu.

```javascript
require('dotenv').config();
```

V horní části souboru otevřete soubor _routes\index.js_ a následující import:

```javascript
var request = require('request');
```

Dále přidejte následující kód přímo pod tento řádek. Tento kód vytvoří koncový bod rozhraní API, který získá ověřovací token Azure AD pomocí vašeho hesla instančního objektu a potom tento token vrátí. Pro načtení subdomény je k dispozici také druhý koncový bod.

```javascript
router.get('/getimmersivereadertoken', function(req, res) {
  request.post ({
          headers: {
              'content-type': 'application/x-www-form-urlencoded'
          },
          url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
          form: {
              grant_type: 'client_credentials',
              client_id: process.env.CLIENT_ID,
              client_secret: process.env.CLIENT_SECRET,
              resource: 'https://cognitiveservices.azure.com/'
          }
      },
      function(err, resp, token) {
          if (err) {
              return res.status(500).send('CogSvcs IssueToken error');
          }

          return res.send(JSON.parse(token).access_token);
      }
  );
});

router.get('/subdomain', function (req, res) {
    return res.send(process.env.SUBDOMAIN);
});
```

Koncový bod rozhraní API **getimmersivereadertoken** by měl být zabezpečený za určitou formou ověřování (například [OAuth](https://oauth.net/2/)), aby se zabránilo neoprávněným uživatelům ve získávání tokenů k použití proti vaší službě s moderní čtečkou a fakturaci. Tato práce překračuje rámec tohoto kurzu.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

1. Otevřete _views\layout.pug_a přidejte následující kód pod `head` `body` značku před značku. Tyto `script` značky načtou [sadu pro moderní čtečku](https://github.com/Microsoft/immersive-reader-sdk) a jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Otevřete _views\index.pug_a nahraďte jeho obsah následujícím kódem. Tento kód naplní stránku nějakým ukázkovým obsahem a přidá tlačítko, které spustí moderní čtečku.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.

        function getImmersiveReaderTokenAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/getimmersivereadertoken',
                    type: 'GET',
                    success: token => {
                        resolve(token);
                    },
                    error: err => {
                        console.log('Error in getting token!', err);
                        reject(err);
                    }
                });
            });
        }

        function getSubdomainAsync() {
            return new Promise((resolve, reject) => {
                $.ajax({
                    url: '/subdomain',
                    type: 'GET',
                    success: subdomain => { resolve(subdomain); },
                    error: err => { reject(err); }
                });
            });
        }

        async function launchImmersiveReader() {
            const content = {
                title: document.getElementById('title').innerText,
                chunks: [{
                    content: document.getElementById('content').innerText + '\n\n',
                    lang: 'en'
                }]
            };

            const token = await getImmersiveReaderTokenAsync();
            const subdomain = await getSubdomainAsync();

            ImmersiveReader.launchAsync(token, subdomain, content);
        }
    ```

3. Naše webová aplikace je teď připravená. Spusťte aplikaci spuštěním:

    ```bash
    npm start
    ```

4. Otevřete prohlížeč a přejděte na _http://localhost:3000_ . Na stránce byste měli vidět výše uvedený obsah. Kliknutím na tlačítko pro **moderní čtečku** spustíte moderní čtečku s vaším obsahem.

## <a name="specify-the-language-of-your-content"></a>Zadejte jazyk vašeho obsahu

Moderní čtečka podporuje řadu různých jazyků. Jazyk vašeho obsahu můžete určit podle následujících kroků.

1. Otevřete _views\index.pug_ a přidejte následující kód pod `p(id=content)` značku, kterou jste přidali v předchozím kroku. Tento kód přidá do stránky obsah pro španělštinu obsahu.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. V kódu JavaScriptu přidejte následující nad volání `ImmersiveReader.launchAsync`. Tento kód předá španělský obsah do moderního čtecího zařízení.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Znovu přejděte _http://localhost:3000_ na. Na stránce byste měli vidět španělský text a když kliknete na **moderní čtečku**, zobrazí se i v moderní čtečce.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Zadejte jazyk rozhraní moderního čtecího zařízení.

Standardně se jazyk moderního čtecího rozhraní shoduje s nastavením jazyka prohlížeče. Jazyk moderního čtecího rozhraní lze také určit pomocí následujícího kódu.

1. V _views\index.pug_nahraďte volání `ImmersiveReader.launchAsync(token, content)` kódem níže.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Přejděte na _http://localhost:3000_ . Při spuštění moderního čtecího zařízení se rozhraní zobrazí ve francouzštině.

## <a name="launch-the-immersive-reader-with-math-content"></a>Spuštění moderního čtečky s využitím matematického obsahu

Do moderního čtecího zařízení můžete zahrnout matematický obsah pomocí formátu [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Upravte _views\index.pug_ tak, aby zahrnoval následující kód nad volání `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Přejděte na _http://localhost:3000_ . Když spustíte moderní čtečku a posuňte se k dolnímu okraji, uvidíte matematický vzorec.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/Microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)
* Zobrazit ukázky kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/advanced-csharp)
