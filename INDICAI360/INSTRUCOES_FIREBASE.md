# IndicAÍ360 — publicar no Firebase Hosting com login

Esta pasta já está pronta para virar um site no Firebase, com uma tela de
login (e-mail/senha) na frente do IndicAÍ360.

```
indicai360-firebase/
├── firebase.json          → configuração do Hosting (aponta pro "target" indicai360)
├── .firebaserc            → qual projeto Firebase usar + qual site é o "indicai360"
└── public/
    ├── index.html          → o app IndicAÍ360 (perguntas + recomendação + PDF)
    └── firebase-config.js  → chaves do projeto Firebase (edite com as reais)
```

---

## Se você já tem um projeto Firebase (o mesmo do Bradesco/SulAmérica)

É esse o seu caso. Bradesco e SulAmérica são **sites diferentes dentro do
mesmo projeto** Firebase — e como o login (Authentication) é do projeto
inteiro (não de cada site), os mesmos usuários que já acessam Bradesco e
SulAmérica também vão conseguir entrar no IndicAÍ360 automaticamente, sem
precisar recadastrar ninguém.

### 1. Criar um novo site de Hosting para o IndicAÍ360

No Firebase Console (o mesmo projeto que já está aberto):

1. No menu à esquerda, clique em **Hosting**.
2. Clique em **Adicionar outro site** (ou "Add another site").
3. Digite um ID para o site, por exemplo `indicai360` (se já estiver em uso
   por outra pessoa no mundo, tente algo como `indicai360-maximo360`).
4. Confirme. Anote a URL que aparece (algo como `https://indicai360.web.app`
   ou `https://indicai360-maximo360.web.app`) — é esse o link final do app.

### 2. Registrar um app Web para pegar as chaves (firebaseConfig)

1. Clique na engrenagem (⚙) ao lado de "Visão geral do projeto" →
   **Configurações do projeto**.
2. Na aba **Geral**, role até **Seus apps**.
3. Se preferir reaproveitar as chaves que o Bradesco ou o SulAmérica já usam,
   pode clicar no app Web já existente e copiar o `firebaseConfig` de lá —
   funciona igual, porque é o mesmo projeto.
4. Se preferir um app próprio (mais organizado), clique no ícone **"</>"**
   (Web) → dê um apelido, ex.: `indicai360-web` → **Registrar app**. Não
   precisa marcar "Configurar também o Firebase Hosting" nessa tela.
5. Copie o bloco `firebaseConfig = { apiKey: "...", authDomain: "...", ... }`
   mostrado na tela.
6. Abra `public/firebase-config.js` desta pasta e substitua os valores que
   começam com `COLOQUE_` pelos valores reais copiados.

> Enquanto os valores continuarem como `COLOQUE_...`, o IndicAÍ360 mostra um
> aviso na tela de login e oferece um botão "Pré-visualizar sem login" —
> assim dá pra conferir o visual antes de configurar o Firebase de verdade.

### 3. Conferir se o login por e-mail/senha já está ativo

Vá em **Authentication → Sign-in method**. Como o Bradesco e o SulAmérica já
usam login, o provedor **E-mail/senha** já deve aparecer como "Ativado" —
não precisa fazer nada. Se não estiver, clique nele e ative.

Os usuários que já existem em **Authentication → Users** já vão conseguir
entrar no IndicAÍ360 direto. Se quiser cadastrar alguém novo só para essa
ferramenta, é na mesma aba, com **Add user**.

### 4. Conectar esta pasta ao projeto e ao site (via terminal)

No computador onde você vai publicar (precisa ter Node.js instalado):

```bash
npm install -g firebase-tools      # só na primeira vez
firebase login                     # entra com a mesma conta Google do console
cd indicai360-firebase

firebase use --add
# escolha o projeto que já tem Bradesco e SulAmérica na lista que aparece
# quando pedir um "alias", pode digitar "default"

firebase target:apply hosting indicai360 SEU-SITE-ID-DO-PASSO-1
# troque SEU-SITE-ID-DO-PASSO-1 pelo ID que você criou no passo 1
# (ex.: indicai360, ou indicai360-maximo360)
```

Esse último comando escreve automaticamente a ligação entre o "target"
`indicai360` (usado no `firebase.json` desta pasta) e o site real que você
criou no Console. Não precisa editar `.firebaserc` na mão.

### 5. Publicar

```bash
firebase deploy --only hosting:indicai360
```

Ao final, a CLI mostra a URL pública — a mesma que você anotou no passo 1.
É esse link que você compartilha com os corretores.

Para publicar novas versões depois de qualquer alteração no `index.html`,
repita só esse último comando na mesma pasta.

---

## Se for começar um projeto Firebase do zero (não é o seu caso agora)

<details>
<summary>Clique para expandir — só use isso se o IndicAÍ360 for ficar num projeto Firebase novo, separado do Bradesco/SulAmérica</summary>

1. Acesse https://console.firebase.google.com e clique em **Adicionar
   projeto**. Dê um nome e conclua a criação (Google Analytics é opcional).
2. Em **Authentication → Sign-in method**, ative **E-mail/senha**. Em
   **Users**, cadastre manualmente cada corretor/usuário autorizado.
3. Em **Configurações do projeto → Geral → Seus apps**, registre um app Web
   (ícone `</>`) e copie o `firebaseConfig` para `public/firebase-config.js`.
4. Abra `.firebaserc` e troque `COLOQUE_SEU_PROJETO` pelo Project ID.
5. Como este `firebase.json` já usa um "target" (`indicai360`), rode:
   ```bash
   npm install -g firebase-tools
   firebase login
   cd indicai360-firebase
   firebase use --add        # escolha o projeto novo
   firebase target:apply hosting indicai360 SEU-PROJETO   # site padrão = ID do projeto
   firebase deploy --only hosting:indicai360
   ```

</details>

---

## Testar localmente antes de publicar (opcional)

```bash
firebase emulators:start --only hosting
```

Abre em `http://localhost:5000` com a mesma configuração do `firebase.json`.

## Perguntas frequentes

**Os usuários do Bradesco/SulAmérica vão conseguir entrar no IndicAÍ360 sem eu fazer nada?**
Sim, se você reaproveitar o mesmo projeto Firebase (é o caso aqui) — o
cadastro de Authentication é do projeto inteiro, não de cada site. Todo
mundo que já está em Authentication → Users entra em qualquer site do
mesmo projeto.

**Posso liberar cadastro público (qualquer pessoa cria conta)?**
Não está configurado assim de propósito — o acesso é restrito aos usuários
que você cadastrar manualmente em Authentication → Users, igual a um painel
interno. Se no futuro quiser abrir cadastro público ou login com Google,
é uma mudança pequena no `firebase-config.js`/`index.html` (posso te ajudar
quando quiser).

**Esqueci a senha de um usuário, e agora?**
Na tela de login existe "Esqueci minha senha", que envia um e-mail de
redefinição pelo próprio Firebase. Você também pode redefinir manualmente
pelo console em Authentication → Users → (usuário) → "Redefinir senha".

**A rede da Amil está completa?**
Foi carregada a partir dos 3 PDFs de rede referenciada enviados (Linha Amil,
Linha Black, Linha Selecionada), cobrindo Bronze SP/SP Mais, Prata, Ouro,
Platinum, Black I/II, S2500/S6500 e a Linha Selecionada (S380/S450/S750).
As grades por adesão (DF) e as variantes Prata II/Ouro II/Platinum Mais não
estavam nesses materiais e por isso aparecem como "rede a confirmar" nas
buscas de hospital.
