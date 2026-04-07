---
title: Como fazer deploy de aplicações Next.js no Wolke.host
description: Guia completo para fazer deploy de aplicações Next.js no Wolke.host, a plataforma de hospedagem moçambicana com pagamento via M-Pesa.
tags:
  - Next.js
  - Wolke.host
  - Deploy
  - Moçambique
---

# Como fazer deploy de aplicações Next.js no Wolke.host

O [Wolke.host](https://wolke.host) é uma plataforma de hospedagem moçambicana que permite fazer deploy de aplicações web directamente a partir do GitHub. Ao contrário de plataformas como Vercel ou Netlify que cobram em dólares americanos, o Wolke factura em Meticais (MZN) e aceita pagamento via M-Pesa, tornando-o acessível a qualquer developer ou empresa em Moçambique.

Neste artigo, vamos percorrer todo o processo desde a criação da conta até ao momento em que a tua aplicação Next.js fica online com um URL público.

---

## Pré-requisitos

Antes de começar, certifica-te de que tens o seguinte:

- Uma aplicação Next.js num repositório GitHub (público ou privado)
- Uma conta de email activa para o registo
- M-Pesa ou PayPal para o pagamento do pacote escolhido

Se ainda não tens um projecto Next.js, cria um rapidamente com:

```bash
npx create-next-app@latest minha-app
cd minha-app
git init && git add . && git commit -m "chore: initial commit"
```

Faz push para um repositório no GitHub antes de continuar.

---

## Criar e activar a conta no Wolke

Acede a [wolke.host](https://wolke.host) e regista a tua conta preenchendo o nome, email e palavra-passe. Após o registo, o Wolke envia um email de confirmação. Clica no link de activação antes de tentares fazer login — sem esse passo o acesso ao dashboard não é possível.

![Formulário de criação de conta no Wolke.host](assets/nextjs-assets/fig-1.png)

*Formulário de registo do Wolke.host*

!!! tip "Dica"
    Se não vires o email na caixa de entrada, verifica a pasta de spam.

---

## Aceder ao dashboard e criar uma App

Depois do login, serás recebido pelo dashboard de **Apps**. Como é a primeira vez, a listagem está vazia e verás a mensagem *"You don't have any app yet."*

Clica no botão **New** para iniciar o processo de deploy e de seguida clica na opção **Create new app**.

![Dashboard vazio com menu New expandido mostrando as opções Create new app e Create new pipeline](assets/nextjs-assets/fig-2.png)

*Dashboard de Apps no primeiro login - clica em New e selecciona Create new app*

---

## Conectar a conta do GitHub

Serás redirecionado para a página **Deploy your app**. O primeiro passo aqui é conectar a tua conta GitHub.

Clica no botão com o logo do GitHub. O GitHub vai pedir permissão para o Wolke aceder aos teus repositórios. Aceita as permissões e volta ao Wolke.

![Página Deploy your app com o botão GitHub Connected a verde](assets/nextjs-assets/fig-3.png)

*Ligação ao GitHub confirmada - botão passa a verde com Github Connected*

O botão passa a mostrar **Github Connected** a verde, o que confirma que a ligação foi estabelecida com sucesso.

---

## Nomear a App e ligar o repositório

Com o GitHub ligado, preenche o campo **App name** com o nome que queres dar à aplicação no Wolke (ex.: `minha-app-nextjs`).

Na secção **Search for a repository to connect to**, segue estes passos:

1. No dropdown do lado esquerdo, selecciona a tua conta GitHub ou organização.
2. No campo de texto, escreve o nome do repositório (ex.: `wolke`).
3. Clica no botão **Search**.
4. Quando o repositório aparecer na lista (ex.: `rnrnshn/wolke`), clica em **Connect**.

![Formulário com repositório rnrnshn/wolke listado e botão Connect visível](assets/nextjs-assets/fig-4.png)

*Repositório encontrado - clica em Connect para ligar ao Wolke*

O botão muda temporariamente para **Connecting...** enquanto a ligação é estabelecida. Após alguns segundos, o painel actualiza-se e mostra o repositório conectado com sucesso.

![Formulário com repositório conectado, branch main seleccionado e botão Enable Automatic Deploys](assets/nextjs-assets/fig-5.png)

*Repositório conectado - branch main seleccionado e opção de deploys automáticos disponível*

!!! tip "Dica"
    Activa **Enable Automatic Deploys** para o Wolke fazer um novo deploy automaticamente a cada push para o branch seleccionado (`main` por defeito). Recomendado para um workflow de CI/CD.

---

## Configurar variáveis de ambiente

Na secção **Add Environment Variables**, adiciona as variáveis que a tua aplicação precisa. Estas são equivalentes ao ficheiro `.env.local` que usas localmente.

**Exemplos de variáveis comuns para Next.js:**

```env
NEXT_PUBLIC_API_URL=https://api.meusite.co.mz
DATABASE_URL=postgresql://user:password@host:5432/db
NEXTAUTH_SECRET=um-segredo-muito-longo-e-aleatorio
```

![Painel de Environment Variables com campos KEY e VALUE](assets/nextjs-assets/fig-6.png)

*Painel de configuração de variáveis de ambiente*

!!! warning "Nota"
    Variáveis com o prefixo `NEXT_PUBLIC_` ficam expostas no browser. Usa-as apenas para valores que podem ser públicos.

---

## Configurar o comando de pré-deploy (opcional)

A secção **Add Pre-Deploy Command** permite definir um comando que corre antes do servidor arrancar. É útil para:

- Correr migrações de base de dados
- Fazer seed de dados iniciais
- Qualquer script de preparação necessário antes da app arrancar

```bash
npx prisma migrate deploy
```

Se a tua aplicação não precisa de nenhuma preparação especial, deixa esta secção em branco.

---

## Escolher o pacote de recursos

O Wolke oferece três pacotes, todos com preços em MZN:

| Pacote | Preço | CPU | RAM | Instâncias |
|--------|-------|-----|-----|------------|
| Launch | 0 MZN/mês | 0.125 | 256 MB | 1 |
| Grow | 350 MZN/mês | 0.250 | 512 MB | 1 |
| Scale | 650 MZN/mês | 0.500 | 1 GB | 1 |

![Tabela de planos Launch, Grow e Scale com preços e recursos](assets/nextjs-assets/fig-7.png)

*Planos disponíveis no Wolke.host com preços em MZN*

### Qual pacote escolher para Next.js?

**Launch (0 MZN/mês):** Adequado para portfólios e MVPs com tráfego baixo.

**Grow (350 MZN/mês):** Boa opção para aplicações pequenas em produção.

**Scale (650 MZN/mês):** Recomendado para aplicações com SSR pesado. O ponto de equilíbrio para a maioria dos casos em produção e para aplicações de alta disponibilidade com muitos utilizadores simultâneos.

!!! tip "Dica"
    Podes sempre mudar de pacote mais tarde nas configurações da app.

---

## Efectuar o pagamento e fazer o Deploy

Com tudo configurado, clica no botão **Deploy** no final da página.

Aparece um modal de **Checkout** com as opções de pagamento disponíveis:

- **Pay with Mpesa** — pagamento directo via M-Pesa (recomendado para Moçambique)
- **PayPal** — para quem prefere cartão internacional

![Modal de Checkout com botões Pay with Mpesa e PayPal](assets/nextjs-assets/fig-8.png)

*Modal de pagamento - M-Pesa, PayPal ou cartão*

Selecciona o método de pagamento da tua preferência e conclui a transacção. O primeiro mês é cobrado no momento do deploy.

!!! tip "Dica"
    O M-Pesa é a forma mais rápida e prática para developers em Moçambique. Tens o valor debitado directamente do teu número sem precisar de cartão.

---

## Acompanhar o deployment e aceder à app

Após o pagamento, o Wolke inicia automaticamente o processo de build e deploy. Serás redirecionado para o dashboard da tua app, onde encontras:

- O nome da app gerado (ex.: `wolke-yfd27vh`)
- O repositório e branch ligados (ex.: `rnrnshn/wolke | main`)
- O URL público da aplicação (ex.: `wolke-yfd27vh.onwolke.app`)

![Dashboard da app com Deployment History mostrando o primeiro deploy como created](assets/nextjs-assets/fig-9.png)

*Dashboard da app após o primeiro deploy - histórico, URL público e botão Manual Deploy visíveis*

Na secção **Deployment History**, podes acompanhar o estado de cada deploy. O primeiro registo aparece como *"created"* com o hash do commit e a mensagem (ex.: `first commit`). Clica em **view logs** para acompanhar o processo de build em tempo real.

Quando o deploy estiver concluído com sucesso, a tua aplicação Next.js fica acessível no URL `.onwolke.app` atribuído pelo Wolke.

!!! tip "Dica"
    Podes fazer um novo deploy manualmente a qualquer momento clicando no botão **Manual Deploy** no canto superior direito do dashboard da app.

---

## Explorar o dashboard da App

O dashboard de cada app tem três tabs principais:

**Overview** — Histórico de deployments, URL público, repositório e branch ligados. É a vista principal que usarás no dia-a-dia.

**Deploy** — Configurações de deploy: branch, variáveis de ambiente e comandos de pré-deploy. Podes actualizar estas configurações a qualquer momento.

**Settings** — Configurações avançadas da app, como mudar o pacote ou gerir o domínio personalizado.

![Dashboard com tabs Overview, Deploy e Settings e URL público da app visível](assets/nextjs-assets/fig-10.png)

*As três tabs principais do dashboard de cada app*

---

## Conclusão

O Wolke.host simplifica consideravelmente o processo de deploy de aplicações Next.js, especialmente para developers moçambicanos que querem evitar os custos em dólares das plataformas internacionais.

O fluxo completo — desde criar conta, ligar GitHub, configurar e pagar via M-Pesa — demora menos de 10 minutos. Com preços em MZN, suporte a deploys automáticos via push e um URL público gerado automaticamente, o Wolke é uma alternativa local a levar a sério no ecossistema tech moçambicano.
