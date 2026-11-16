# Radar de apartamentos

Monitor de anúncios de apartamentos (compra, aluguel e leilão) em bairros de São Paulo, com avisos no WhatsApp e
um painel publicado pelo GitHub Pages. Roda de graça no GitHub Actions; não precisa de servidor.

- **Fontes:** ZAP Imóveis, Viva Real e QuintoAndar (venda e aluguel); Portal Zuk, Mega Leilões e a lista oficial da
  Caixa (leilões e imóveis retomados). Cada lote de leilão vem com lances e datas do 1º/2º leilão, custo total
  estimado (comissão, ITBI, registro), comparação com os anúncios do bairro e com as vendas do prédio no ITBI, e o
  aviso de ocupado/desocupado — critérios próprios na seção `leiloes` do `config.yaml`.
- **Avisos:** anúncio novo dentro dos seus critérios, queda de preço, imóvel bem abaixo da mediana do bairro e
  fonte com erro. Aluguel pode avisar a cada coleta; compra num resumo diário. Destaques ⭐: andar alto, vista
  livre, perto de parque, piscina privativa e churrasqueira do apartamento.
- **Painel:** resumo do mercado, gráfico preço × área, comparação entre bairros, tabela de anúncios com
  duplicados agrupados, transações reais registradas no ITBI da Prefeitura e um simulador comprar × alugar com
  financiamento, FGTS e projeções do Boletim Focus.

**Começando agora?** Siga o [PRIMEIROS-PASSOS.md](PRIMEIROS-PASSOS.md): ele tem o roteiro e as instruções para
colar no Claude Code, que configura tudo para os seus bairros e preferências.

Para ver o painel com dados fictícios antes de instalar: abra `docs/index.html?dados=demo.json` num servidor
local (`cd docs && python -m http.server`).

## Instalação (uns 20 minutos)

### 1. Crie o seu repositório
No GitHub, **Use this template → Create a new repository** (pode ser privado ou público; o painel no GitHub
Pages exige repositório público no plano gratuito).

### 2. Ajuste os critérios
Edite o [`config.yaml`](config.yaml): bairros, orçamento, área mínima, quartos, o que conta como destaque, como
quer receber compra e aluguel, e `painel_url` (o endereço do seu GitHub Pages). Cada opção está comentada.

- **Bairros:** use o nome como aparece no ZAP/Viva Real.
- **QuintoAndar:** a busca dele é por retângulo no mapa. Para bairros fora de Itaim Bibi, Vila Nova Conceição e
  Jardim Paulista, desenhe a sua **área de interesse** (passo 6) — com ela ativa, o QuintoAndar cobre o polígono
  inteiro — ou peça ao Claude para incluir o retângulo do bairro em `imoveis/sources/quintoandar.py`.
- **Transações (ITBI):** em `transacoes.bairros`, liste os seus bairros e, em `prefixos_cep`, os 3 primeiros
  dígitos dos CEPs deles.
- **Parques:** `imoveis/parques.json` traz o Parque Ibirapuera e o Parque do Povo (contornos do OpenStreetMap).
  Para outros parques, peça ao Claude (ele busca o contorno pelo workflow de sondagem).

### 3. Dê permissão de escrita ao robô
*Settings → Actions → General → Workflow permissions →* **Read and write permissions** → Save.
(Cada coleta grava o estado e o painel no próprio repositório.)

### 4. Configure o WhatsApp (CallMeBot)
1. Salve nos contatos o número do CallMeBot indicado em
   [callmebot.com → WhatsApp API](https://www.callmebot.com/blog/free-api-whatsapp-messages/) (ele muda de
   tempos em tempos; use o da página).
2. Mande para esse contato, pelo WhatsApp: `I allow callmebot to send me messages`.
3. Você recebe uma *apikey*. Em *Settings → Secrets and variables → Actions → New repository secret*, crie:
   - `CALLMEBOT_PHONE`: seu número com DDI e DDD, ex.: `+5511999998888`
   - `CALLMEBOT_APIKEY`: a apikey recebida

   Para avisar mais de uma pessoa: números e apikeys separados por vírgula, na mesma ordem.

O CallMeBot corta mensagens acima de ~730 caracteres; o monitor já divide os avisos em mensagens de até 600.
Alternativas (podem ser usadas juntas): **ntfy** (secret `NTFY_TOPIC`), **Telegram** (`TELEGRAM_BOT_TOKEN` e
`TELEGRAM_CHAT_ID`) ou **e-mail** (`SMTP_USER`, `SMTP_PASSWORD` com senha de app do Gmail, `EMAIL_TO`).

### 5. Teste e faça a primeira coleta
1. *Actions →* **Monitor de imóveis** *→ Run workflow*, marcando **"Só enviar uma mensagem de teste"**. Confira se
   chegou no WhatsApp.
2. Rode de novo sem marcar nada. A primeira coleta é a **carga inicial**: registra o que já está anunciado, sem
   disparar avisos, e manda "Monitor de imóveis ativado". Daí em diante, só novidades.
3. *Actions →* **Transações (ITBI)** *→ Run workflow* para carregar o histórico de transações (alguns minutos).
4. Ligue o painel: *Settings → Pages → Source:* **Deploy from a branch**, branch principal, pasta **/docs**.

### 6. (Opcional) Desenhe a sua área de interesse
Abra `https://SEU-USUARIO.github.io/SEU-REPOSITORIO/area.html`, arraste os pontos, clique em **Copiar
coordenadas** e cole em `area_de_interesse.poligono` no `config.yaml`, com `ativo: true`. A área vira filtro dos
avisos, aparece no painel ("Só na minha área") e amplia a busca do QuintoAndar. Para marcar um ponto de
referência (trabalho, escola) com círculos de 10/15/20 minutos a pé, preencha `HOME` no início do script de
`docs/area.html`.

### 7. Agende as coletas
O agendador do próprio GitHub atrasa e pula execuções, então o workflow só tem uma execução de reserva por dia
(1h47 de Brasília). Para coletar a cada 2 horas, use o [cron-job.org](https://cron-job.org) (gratuito):

1. No GitHub, crie um token em *Settings (da sua conta) → Developer settings → Fine-grained tokens*, só para este
   repositório, com permissão **Actions: Read and write**.
2. No cron-job.org, crie um job:
   - URL: `https://api.github.com/repos/SEU-USUARIO/SEU-REPOSITORIO/actions/workflows/monitor.yml/dispatches`
   - Método **POST**; cabeçalhos `Authorization: Bearer SEU_TOKEN`, `Accept: application/vnd.github+json` e
     `Content-Type: application/json`; corpo `{"ref":"main"}` (o nome do seu branch principal).
   - Horário: a cada 2 horas (por exemplo, das 5h17 às 21h17). O GitHub responde **204** quando dá certo.

   Dica: no cron-job.org, a opção "Import from curl" com o comando abaixo preenche tudo de uma vez.
   ```
   curl -X POST -H "Authorization: Bearer SEU_TOKEN" -H "Accept: application/vnd.github+json" \
     -H "Content-Type: application/json" -d '{"ref":"main"}' \
     https://api.github.com/repos/SEU-USUARIO/SEU-REPOSITORIO/actions/workflows/monitor.yml/dispatches
   ```
3. Os tokens expiram: anote a data e renove antes.

## Adaptando com o Claude
O [`CLAUDE.md`](CLAUDE.md) descreve o projeto, o que é regra de negócio e como verificar mudanças; com o Claude
Code aberto no seu repositório, peça em português mesmo: "troque os bairros para Pinheiros e Vila Madalena",
"aluguel até 12 mil", "avise compra a cada coleta em vez do resumo", "inclua o Parque Villa-Lobos como destaque".
Os sites dos portais e da Prefeitura costumam bloquear acesso direto de fora; o projeto tem o workflow
**Sondagem dos sites** (entrada `modulo`) para testar coisas a partir do GitHub Actions.

## Como funciona
- `imoveis/` — coleta (Python): um coletor por portal em `imoveis/sources/`, critérios em `imoveis/common.py`,
  ciclo, avisos e resumo diário em `imoveis/__main__.py`, mensagens em `imoveis/notify.py`, duplicados em
  `imoveis/dedupe.py`, abaixo da mediana em `imoveis/bargain.py`, destaques em `imoveis/details.py`, área e
  parques em `imoveis/geo.py`, ITBI em `imoveis/itbi.py`, Boletim Focus em `imoveis/macro.py`.
- `data/` — estado e histórico (gravados pelo robô; não edite à mão).
- `docs/` — painel (HTML e JavaScript, sem build): `index.html`, simulador em `sim.js`, transações em `itbi.js` e
  `transacoes.js`, mapa da área em `area.html`; dados em `dados.json` e `transacoes.json`.
- `.github/workflows/` — `monitor.yml` (coleta), `itbi.yml` (transações, dias 4 e 19), `probe.yml` (sondagens).
- `diagnostico/itbi/RELATORIO.md` — como o fator de área 0,72 e o mapeamento CEP → bairro foram calibrados.

**Leitura dos números:** anúncios são preços *pedidos*, não fechados; o mesmo imóvel pode estar em mais de um
portal (o painel agrupa); a aba Transações mostra valores de fechamento declarados no ITBI.

## Se um site parar de funcionar
Os portais mudam a API de tempos em tempos. Ao receber "fonte com erro", rode o **Monitor de imóveis** com
**"Salvar respostas brutas"** marcado, baixe o artefato `debug` e use (ou entregue ao Claude) para ajustar o
coletor em `imoveis/sources/`.

## Rodando localmente
```bash
pip install -r requirements.txt
python -m imoveis --dry-run     # mostra o que encontraria, sem notificar nem salvar
python -m pytest tests          # testes
node tests/js/sim.test.mjs && node tests/js/itbi.test.mjs
```
