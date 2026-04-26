# UFV Dashboard — Anomalias Térmicas

Dashboard online para visualização de inspeções termográficas de Usinas Fotovoltaicas.

## O que tem na pasta

```
UFV-Dashboard/
├── index.html       ← o site
├── usinas.json      ← dados de todas as usinas
└── README.md        ← este arquivo
```

## Como colocar online (GitHub Pages, grátis, 5 minutos)

### Passo 1 — Criar conta GitHub
Se ainda não tem, vá em https://github.com/signup e crie uma conta.

### Passo 2 — Criar o repositório
1. Logado no GitHub, clique em **+** no canto superior direito → **New repository**
2. Nome do repo: algo como `ufv-dashboard` (sem espaços)
3. Marque **Public** (precisa ser público para o GitHub Pages funcionar de graça)
4. **Não** marque "Add a README" (já temos um)
5. Clique em **Create repository**

### Passo 3 — Subir os arquivos
Na página do novo repo (vazio), clique em **uploading an existing file** (ou arraste).
Arraste estes 3 arquivos:
- `index.html`
- `usinas.json`
- `README.md`

Clique em **Commit changes**.

### Passo 4 — Ativar o GitHub Pages
1. No repo, vá em **Settings** (aba no topo)
2. No menu lateral esquerdo, clique em **Pages**
3. Em "Source", selecione **Deploy from a branch**
4. Em "Branch", selecione **main** e pasta **/ (root)**
5. Clique em **Save**

Em ~1 minuto seu site estará no ar em:
```
https://SEU-USUARIO.github.io/ufv-dashboard/
```

Esse é o link que você manda pro cliente.

---

## Como adicionar uma nova usina

1. Abra o seu site (no link acima)
2. Clique em **+ Adicionar Usina** (canto superior direito)
3. Digite o nome da usina (ex: "UFV Sol do Sertão")
4. Arraste a planilha `.xlsx` da inspeção
5. Confira a prévia e clique em **Baixar JSON atualizado**
6. Um arquivo `usinas.json` será baixado
7. Vá no seu repositório GitHub, clique em `usinas.json`
8. Clique no ícone de **lápis** (✏ Edit)
9. Apague todo o conteúdo e cole o conteúdo do novo arquivo baixado (ou use a função "Replace this file" — clique nos 3 pontinhos no canto superior do arquivo)
10. Commit changes
11. Em ~30 segundos a nova usina aparecerá no dropdown do site

> **Mais fácil ainda:** Na página do repo, arraste o `usinas.json` baixado direto pra cima do antigo — ele vai te perguntar se quer substituir.

## ⚡ Auto-sync com Google Sheets (configurado)

A usina "UFV Nova Granada 2" está conectada diretamente à planilha:
https://docs.google.com/spreadsheets/d/1XK86NsxCx4_djNfpQp6DQueUgrm02iWFt8czGwq2dmg

**O que isso significa:** quando a equipe edita o status na aba "Equipe de Campo" da planilha Google, o site:
- Atualiza automaticamente a cada **5 minutos** (auto-refresh)
- Pode ser atualizado **na hora** clicando no botão "Atualizar" no canto superior direito
- Sempre carrega a versão mais recente quando alguém abre o site

**Pré-requisito:** a planilha precisa estar compartilhada como **"Qualquer pessoa com o link pode visualizar"**:
1. Abre a planilha no Google Sheets
2. Botão "Compartilhar" (canto superior direito)
3. Em "Acesso geral" → mude de "Restrito" para **"Qualquer pessoa com o link"** (papel: Leitor)
4. Pronto

> **Dica de segurança:** o link público é só de leitura — ninguém consegue editar pela URL. Apenas pessoas que você adicionar explicitamente na lista do Compartilhar conseguem editar.

### Para conectar uma usina nova ao Sheets:

Edite o arquivo `index.html` e procure por `SHEETS_CONFIG = {`. Adicione um novo bloco:
```js
'id-da-nova-usina': {
  sheetId: 'COLE_O_ID_DA_PLANILHA_AQUI',
  gidAnomalias: 'GID_DA_ABA_ANOMALIAS',
  gidCampo: 'GID_DA_ABA_EQUIPE_CAMPO'
}
```

O `sheetId` está na URL da planilha (`/spreadsheets/d/AQUI/`). Os GIDs aparecem na URL quando você clica em cada aba (`gid=NUMERO`).

---

## Atualizando status da equipe de campo (modo manual / sem Sheets)

A planilha tem duas abas:
- **`usina2_anomalias_v1`** — anomalias detectadas (não mexer)
- **`Equipe Campo`** — controle de execução (a equipe preenche)

Colunas que a equipe preenche na aba "Equipe Campo":
- **Status**: `Resolvido` / `Em andamento` / `Pendente` / `Não procede`
- **Data da Correção**: data em que executou
- **Problema Identificado**: descrição do que encontrou

**Fluxo de atualização:**
1. A equipe atualiza a planilha no Drive (preenche os status)
2. Você baixa a planilha atualizada
3. No site, clica em **+ Adicionar Usina** → arrasta o XLSX → o site lê as duas abas e mostra o progresso na aba **Correções de Campo**
4. Baixa o `usinas.json` novo → sobe no GitHub → cliente vê

**O que muda automaticamente quando uma anomalia vira "Resolvido":**
- Aparece em verde no mapa
- Sai dos cálculos de perda na aba Performance (a saúde da usina sobe)
- Aparece como tratada nos KPIs da aba Correções de Campo

---

## Formato esperado da planilha

A planilha `.xlsx` precisa ter estas colunas (exatamente esses nomes na primeira linha):

| Coluna | Descrição |
|---|---|
| ID | Número sequencial da anomalia |
| FileName | Nome do arquivo da foto |
| Foto | Caminho/nome da foto (opcional) |
| Anomalies | Tipo: hot-spot, multi hot-spot, shadow, open diode, dirty module |
| Module | Número do módulo |
| String | Identificação da string |
| Tracker | Identificação do tracker |
| Inverter | Inversor (ex: INV-1.06) |
| Skid | Skid (ex: SKID 1) |
| X, Y | Coordenadas locais (UTM) |
| TempMax | Temperatura máxima detectada (°C) |
| TempMin | Temperatura mínima (°C) |
| Delta | ΔT (diferença térmica) |
| Latitude | GPS latitude (decimal, negativa pro hemisfério sul) |
| Longitude | GPS longitude (decimal, negativa pro oeste) |

---

## Recursos do dashboard

- **Mapa interativo** com imagens de satélite — cada anomalia é um ponto, cor indica tipo, tamanho indica gravidade do ΔT
- **Filtros**: tipo de anomalia, inversor, skid, ΔT mínimo
- **5 cards de estatísticas**: total, críticas, moderadas, T.Max, ΔT máximo
- **4 gráficos**: tipos (donut), por skid, por inversor, distribuição de ΔT
- **Tabela ordenável** — clica no cabeçalho pra ordenar; clica numa linha pra dar zoom no mapa
- **Selector de usinas** no topo — alterna entre todas as inspeções

---

## Testando localmente antes de publicar

Abrir `index.html` direto no navegador NÃO vai funcionar (CORS bloqueia o `fetch` do JSON em arquivos `file://`). Pra testar local:

**Opção 1 — Python (já vem no seu PC):**
```bash
cd UFV-Dashboard
python -m http.server 8000
```
Depois abre http://localhost:8000

**Opção 2 — Só publica direto no GitHub Pages e testa lá.**

---

## Customização (opcional)

- **Cor do tema**: edite o `<header>` no CSS (linha do `linear-gradient`)
- **Tipos de anomalia novos**: adicione no objeto `ANOMALIA_COLOR` no script
- **Logo**: substitua o emoji `☀` por uma `<img>` no `<header>`

---

Dúvidas? Manda mensagem.
