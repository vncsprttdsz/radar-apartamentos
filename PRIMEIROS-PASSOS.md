# Primeiros passos

Roteiro para ter o seu radar funcionando, com o Claude Code fazendo a parte de código. Você precisa de uma conta
no GitHub e de acesso ao Claude Code (em [claude.ai/code](https://claude.ai/code), no app ou no terminal).

## 1. Crie o seu repositório (2 min)
1. Neste repositório, clique em **Use this template → Create a new repository**.
2. Dê um nome (ex.: `radar-apartamentos`) e escolha **Public** se quiser o painel no GitHub Pages de graça
   (repositório privado só publica o Pages em planos pagos). Não há dados pessoais no código; as suas chaves
   ficam nos Secrets, que nunca aparecem no repositório.

## 2. Abra o Claude Code no seu repositório (2 min)
1. Em [claude.ai/code](https://claude.ai/code), conecte a sua conta do GitHub (se ainda não estiver) e escolha
   o repositório que você acabou de criar.
2. Cole o texto da seção **"Instruções para o Claude"** abaixo e responda às perguntas dele.

## 3. Faça no GitHub o que só você pode fazer (15 min)
O Claude vai te guiar, mas estes passos são seus (envolvem senhas e configurações da conta). Detalhes no
[README](README.md#instalação-uns-20-minutos):

1. **Permissão de escrita:** *Settings → Actions → General → Workflow permissions →* **Read and write**.
2. **WhatsApp:** cadastre-se no CallMeBot e crie os secrets `CALLMEBOT_PHONE` e `CALLMEBOT_APIKEY` em
   *Settings → Secrets and variables → Actions*. **Nunca cole a apikey na conversa com o Claude.**
3. **Teste:** *Actions → Monitor de imóveis → Run workflow* marcando "Só enviar uma mensagem de teste".
4. **Primeira coleta:** rode de novo sem marcar nada (é a carga inicial: registra o que já existe, sem avisos).
5. **Transações:** *Actions → Transações (ITBI) → Run workflow*.
6. **Painel:** *Settings → Pages →* Deploy from a branch, `main`, pasta `/docs`.
7. **Agenda:** crie o job no [cron-job.org](https://cron-job.org) com um token do GitHub só deste repositório
   (passo 7 do README). Anote quando o token expira.

## 4. Use
- Avisos chegam no WhatsApp; o painel fica em `https://SEU-USUARIO.github.io/SEU-REPOSITORIO/`.
- Para mudar qualquer coisa, abra o Claude Code no repositório e peça em português: "aluguel até 12 mil",
  "inclua Moema", "avise compra a cada coleta", "destaque também imóveis perto do Parque Villa-Lobos".
- Se chegar "fonte com erro", peça ao Claude para investigar: ele sabe usar o workflow de sondagem.

---

## Instruções para o Claude

Copie tudo entre as linhas e cole como primeira mensagem no Claude Code, com o seu repositório aberto:

```
Este repositório é um monitor de anúncios de apartamentos que vou configurar para mim. Leia o README.md,
o PRIMEIROS-PASSOS.md, o CLAUDE.md e o config.yaml antes de começar.

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
   mostre um resumo do que mudou antes de publicar.
4. As mudanças precisam chegar ao branch principal (main), que é de onde rodam a coleta e o painel. Se você
   trabalhar em outro branch, abra um pull request e me diga para fazer o merge.
5. Depois, me guie pelos passos manuais do PRIMEIROS-PASSOS.md (permissões, secrets, teste, primeira coleta,
   ITBI, GitHub Pages e cron-job.org), um de cada vez, conferindo comigo cada um. Nunca me peça para colar
   tokens, apikeys ou senhas na conversa: eles vão só nos Secrets do GitHub ou no cron-job.org.
```
