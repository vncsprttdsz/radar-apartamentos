# Primeiros passos (para quem nunca usou o GitHub)

Este roteiro leva uns 40 minutos, com pausas para esperar o robô rodar. O Claude faz a parte de código; você
faz os cliques que envolvem a sua conta e as suas senhas. Faça na ordem; cada passo diz o que você deve ver no
final. Se algo sair diferente, copie a mensagem de erro e cole na conversa com o Claude.

**Do que você precisa:** um computador (o celular serve para receber os avisos, mas a configuração é mais fácil
no computador), um e-mail, o seu WhatsApp e uma assinatura do Claude que inclua o Claude Code (planos Pro ou Max).

## 0. Cinco palavras que vão aparecer

| Palavra | O que é |
|---|---|
| **Repositório** ("repo") | A pasta do projeto no GitHub: código, configuração e os dados que o robô grava. |
| **Commit** | Uma alteração salva no repositório, com descrição. O robô faz um commit a cada coleta. |
| **Branch** e **pull request** | Branch é uma cópia de trabalho; pull request (PR) é o pedido para levar as mudanças de um branch para o principal (`main`). Você aprova clicando em "Merge". |
| **Actions** (workflows) | Os "robôs" do GitHub: rodam a coleta de anúncios, mandam os avisos e atualizam o painel. |
| **Secrets** | Cofre do repositório para senhas e chaves. Ninguém vê o conteúdo depois de salvo, nem o Claude. |

## 1. Crie a sua conta no GitHub (5 min)
1. Entre em [github.com/signup](https://github.com/signup) e crie a conta (e-mail, senha e um nome de usuário).
   O nome de usuário vai aparecer no endereço do seu painel: `https://SEU-USUARIO.github.io/...`.
2. Confirme o e-mail. O plano gratuito basta.

## 2. Copie o projeto para a sua conta (2 min)
1. Abra o link do modelo que você recebeu (`github.com/.../radar-apartamentos`), já logado.
2. No canto superior direito, clique no botão verde **Use this template** → **Create a new repository**.
   (Se o botão não aparecer, peça para quem te mandou o link liberar o seu acesso.)
3. Preencha:
   - **Owner:** a sua conta.
   - **Repository name:** por exemplo `radar-apartamentos`.
   - **Public** (público): é o que permite publicar o painel de graça. O código não tem nenhum dado pessoal; as
     suas chaves vão para os Secrets, que não aparecem para ninguém.
4. Clique em **Create repository**.

**Resultado:** você está na página do seu repositório, com a lista de arquivos (`config.yaml`, `README.md`,
pastas `imoveis`, `docs`...). Guarde esse endereço.

## 3. Libere o robô para gravar (1 min)
1. No seu repositório, clique na aba **Settings** (engrenagem, na barra de abas do repositório; em tela pequena
   ela pode estar no menu "...").
2. Na coluna da esquerda: **Actions → General**.
3. Role até **Workflow permissions**, marque **Read and write permissions** e clique em **Save**.
4. Clique na aba **Actions** (no topo). Se aparecer um botão como "I understand my workflows, go ahead and enable
   them", clique nele.

**Resultado:** na aba Actions aparecem, à esquerda, "Monitor de imóveis", "Transações (ITBI)" e "Sondagem dos sites".

## 4. Configure com o Claude (10–15 min)
1. Abra [claude.ai/code](https://claude.ai/code). Na primeira vez, ele pede para conectar o GitHub: autorize e,
   quando perguntar quais repositórios, escolha **Only select repositories** e marque o seu `radar-apartamentos`.
2. Escolha o seu repositório e comece uma sessão nova.
3. Cole o texto da seção **"Instruções para o Claude"** (no fim desta página) e envie.
4. Responda às perguntas dele (bairros, orçamento, o que é importante para você...). Pode responder do seu jeito,
   em português comum.
5. No fim, ele avisa que as mudanças estão prontas. Normalmente vêm num **pull request**:
   - no GitHub, aba **Pull requests** → clique no pedido aberto → **Merge pull request** → **Confirm merge**;
   - se o Claude disser que já publicou direto no `main`, não há nada a fazer.

**Resultado:** o `config.yaml` do seu repositório tem os seus bairros e preferências (abra o arquivo para conferir).

## 5. Ligue o WhatsApp (5 min)
Os avisos usam o CallMeBot, um serviço gratuito que manda mensagens para o seu próprio WhatsApp.

1. Abra [a página do CallMeBot](https://www.callmebot.com/blog/free-api-whatsapp-messages/) e salve nos contatos
   do celular o número indicado lá (ele muda de tempos em tempos; use sempre o da página).
2. Pelo WhatsApp, mande para esse contato exatamente: `I allow callmebot to send me messages`
3. Em alguns minutos chega uma resposta com a sua **apikey** (um número). Guarde.
4. No GitHub, no seu repositório: **Settings → Secrets and variables → Actions → New repository secret**.
   - **Name:** `CALLMEBOT_PHONE` · **Secret:** seu número com +55 e DDD, sem espaços (ex.: `+5511999998888`) →
     **Add secret**.
   - De novo **New repository secret** → **Name:** `CALLMEBOT_APIKEY` · **Secret:** a apikey → **Add secret**.

**Nunca cole a apikey, senhas ou tokens na conversa com o Claude.** Eles só vão nos Secrets (e, no passo 9, no
cron-job.org).

**Resultado:** a página de Secrets lista `CALLMEBOT_PHONE` e `CALLMEBOT_APIKEY` (sem mostrar o conteúdo).

## 6. Teste o aviso e faça a primeira coleta (20 min, quase tudo esperando)
1. Aba **Actions** → clique em **Monitor de imóveis** (à esquerda) → botão **Run workflow** (à direita) →
   deixe o branch `main`, marque **"Só enviar uma mensagem de teste"** → botão verde **Run workflow**.
2. Atualize a página depois de uns segundos: aparece uma linha com um círculo amarelo (rodando). Em 1–2 minutos
   ele vira ✓ verde, e a mensagem de teste chega no WhatsApp.
   - ✗ vermelho ou nada no WhatsApp: clique na linha, depois em "monitor", copie o texto do erro e mostre ao
     Claude. O mais comum é número ou apikey digitados errado nos Secrets.
3. Rode de novo **sem marcar nada**. Essa é a **carga inicial**: o robô registra tudo o que já está anunciado,
   sem te avisar de cada um (seriam centenas). Leva uns 15–20 minutos e termina com a mensagem
   "✅ Monitor de imóveis ativado".
4. Depois: **Actions → Transações (ITBI) → Run workflow → Run workflow**. Ele baixa as transações registradas pela
   Prefeitura (uns 10 minutos). Daí em diante roda sozinho duas vezes por mês.

## 7. Publique o painel (3 min)
1. **Settings → Pages** (coluna da esquerda).
2. Em **Build and deployment → Source**, escolha **Deploy from a branch**. Em **Branch**, escolha `main` e a pasta
   `/docs` → **Save**.
3. Espere 1–2 minutos e atualize: aparece no topo "Your site is live at `https://SEU-USUARIO.github.io/radar-apartamentos/`".
   Abra no computador e no celular (vale salvar na tela inicial do celular).

## 8. (Opcional) Desenhe a sua área no mapa (5 min)
No painel, abra o link **Área no mapa** (ou `.../area.html`). Arraste os pontos azuis para contornar a região que
te interessa, clique em **Copiar coordenadas** e cole na conversa com o Claude pedindo para ativar a área. Com a
área ativa, os avisos só valem para imóveis dentro dela, e o QuintoAndar passa a cobrir a região inteira.

## 9. Coletas a cada 2 horas (10 min)
O GitHub tem um agendador próprio, mas ele atrasa e pula horários. Por isso o robô só tem uma coleta garantida por
dia (1h47 da manhã). Para coletar a cada 2 horas, um serviço gratuito, o cron-job.org, "aperta o botão Run
workflow" por você. Para isso ele precisa de uma chave (token) que só permite disparar os robôs deste repositório.

**a) Crie o token no GitHub**
1. Clique na sua foto (canto superior direito) → **Settings** (agora as configurações da sua conta).
2. Na coluna da esquerda, lá embaixo: **Developer settings → Personal access tokens → Fine-grained tokens →
   Generate new token**.
3. Preencha:
   - **Token name:** `cron-job radar`.
   - **Expiration:** o prazo mais longo que aparecer (ex.: 1 ano). **Anote a data na agenda**: quando expirar, as
     coletas de 2 em 2 horas param e você gera outro.
   - **Repository access:** **Only select repositories** → escolha o seu `radar-apartamentos`.
   - **Permissions → Repository permissions → Actions:** **Read and write**.
4. **Generate token** e copie o código que começa com `github_pat_`. Ele aparece uma vez só: deixe a aba aberta até
   terminar o passo b.

**b) Crie o agendamento no cron-job.org**
1. Crie uma conta gratuita em [cron-job.org](https://cron-job.org) e entre.
2. **Cronjobs → Create cronjob**. Procure a opção **Import from curl** (importar de um comando) e cole o texto
   abaixo, trocando `SEU_TOKEN`, `SEU-USUARIO` e, se mudou, `radar-apartamentos`:
   ```
   curl -X POST -H "Authorization: Bearer SEU_TOKEN" -H "Accept: application/vnd.github+json" -H "Content-Type: application/json" -d '{"ref":"main"}' https://api.github.com/repos/SEU-USUARIO/radar-apartamentos/actions/workflows/monitor.yml/dispatches
   ```
   Se não achar o "Import from curl", preencha à mão: URL acima (a que começa com `https://api.github.com`),
   método **POST**, os três cabeçalhos (`Authorization: Bearer SEU_TOKEN`, `Accept: application/vnd.github+json`,
   `Content-Type: application/json`) e o corpo `{"ref":"main"}`.
3. **Horário:** a cada 2 horas, por exemplo das 5h às 21h (fuso de São Paulo). Evite minuto 00: use algo como
   **17** (5h17, 7h17...), porque o GitHub fica congestionado na virada da hora.
4. Salve e use **Test run**: a resposta certa é **204** (quer dizer "recebido, sem conteúdo"). Na aba Actions do
   GitHub aparece uma coleta nova rodando.
   - **401:** token errado ou expirado. **404:** nome de usuário/repositório errado ou o token não tem acesso a ele.
     **422:** branch errado no corpo (`main`). **400:** cabeçalhos ou corpo mal preenchidos; tente o "Import from curl".

## 10. Dia a dia
- **Avisos:** chegam no WhatsApp conforme as suas escolhas (por exemplo, aluguel na hora e compra num resumo
  diário à noite). Cada aviso traz o link do anúncio.
- **Painel:** o seu endereço do GitHub Pages; atualiza a cada coleta.
- **Mudar alguma coisa:** abra o [claude.ai/code](https://claude.ai/code), escolha o repositório e peça em
  português: "aluguel até 12 mil", "inclua Moema", "quero o resumo de compra às 8 da manhã", "destaque imóveis perto
  do Parque Villa-Lobos". Depois faça o merge do pull request (passo 4.5).
- **Mensagem "⚠️ fonte com erro":** um portal mudou ou bloqueou. Peça ao Claude: "chegou fonte com erro do ZAP,
  investigue". Os outros portais continuam funcionando enquanto isso.
- **Parou de chegar tudo:** veja a aba Actions. Sem coletas novas, provavelmente o token do cron-job.org expirou
  (passo 9a). Coletas com ✗ vermelho: mostre o erro ao Claude.

---

## Instruções para o Claude

Copie tudo o que está dentro da caixa abaixo e cole como primeira mensagem no Claude Code, com o seu repositório
escolhido:

```
Este repositório é um monitor de anúncios de apartamentos que vou configurar para mim. Não tenho familiaridade
com GitHub: explique cada passo em linguagem simples, diga exatamente onde clicar e confirme comigo antes de
seguir. Leia o README.md, o PRIMEIROS-PASSOS.md, o CLAUDE.md e o config.yaml antes de começar.

1. Me entreviste, uma pergunta por vez e com sugestões de resposta, sobre:
   - cidade e bairros que me interessam (e se quero restringir a uma área desenhada no mapa);
   - compra, aluguel ou os dois; orçamento de cada um (no aluguel, valor total com condomínio e IPTU);
   - área mínima e quartos mínimos para os avisos;
   - o que é destaque para mim (andar alto, vista livre, parque próximo — qual —, piscina, churrasqueira)
     e se um imóvel novo só deve avisar quando tiver destaque;
   - como receber: aluguel a cada coleta ou num resumo diário; compra no resumo diário (e a que horas);
   - queda de preço que merece aviso (%) e se quero aviso de imóvel bem abaixo da mediana do bairro;
   - por qual canal (WhatsApp/CallMeBot, Telegram, ntfy ou e-mail).
2. Com as respostas, ajuste o config.yaml (inclusive painel_url, transacoes.bairros e prefixos_cep) e o que
   mais for preciso no código para os meus bairros: o QuintoAndar busca por retângulo (VIEWPORTS em
   imoveis/sources/quintoandar.py) e só cobre bairros sem retângulo se a área de interesse estiver ativa;
   parques diferentes do Ibirapuera e do Parque do Povo precisam de contorno em imoveis/parques.json.
   Os sites dos portais, da Prefeitura e do OpenStreetMap costumam bloquear acesso direto daqui: use o
   workflow "Sondagem dos sites" (entrada modulo, scripts em scripts/) para buscar dados pelo GitHub Actions.
3. Rode os testes (python -m pytest -q tests; node tests/js/sim.test.mjs; node tests/js/itbi.test.mjs) e
   me mostre, em poucas linhas e sem jargão, o que mudou antes de publicar.
4. As mudanças precisam chegar ao branch principal (main), que é de onde rodam a coleta e o painel. Se você
   trabalhar em outro branch, abra um pull request e me explique como fazer o merge.
5. Depois, me guie pelos passos 5 a 9 do PRIMEIROS-PASSOS.md (WhatsApp, teste, primeira coleta, ITBI, painel
   e cron-job.org), um de cada vez, esperando eu confirmar cada um. Quando eu colar um erro, explique a causa
   e o que fazer. Nunca me peça para colar tokens, apikeys ou senhas na conversa: eles vão só nos Secrets do
   GitHub ou no cron-job.org.
```
