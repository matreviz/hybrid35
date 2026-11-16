# Projeto Treinos Híbridos — README de contexto

Documento de contexto para **qualquer IA que continue este projeto**. Leia isto antes de mexer
em qualquer arquivo. Ele descreve o que foi construído, por quê, onde estão as coisas e quais
regras não podem ser quebradas.

---

## 1. Objetivo do projeto

Entregar **web apps de treino auto-contidos** (um único `index.html` + manifest + ícones),
hospedados no **GitHub Pages** e **instalados como app (PWA)** no celular:

1. **App principal (dono do projeto)** — homem, 31 anos, voltando a treinar depois de um tempo
   parado, com histórico de **dor lombar**. Tem academia disponível. Quer um programa híbrido
   (musculação + CrossFit + Hyrox), com treinos de **40 a 45 min**, e **proteção da lombar nas
   primeiras 5 sessões**.
2. **App da Ana (esposa)** — app separado no mesmo repositório, feito para **iOS**. Contexto:
   bebê de 1 ano, liberada pela médica, voltando a treinar regularmente (cuidados pós-parto
   relaxados).

Requisitos transversais:
- Abrir de forma confiável no Android (e iOS no caso da Ana).
- **Salvar o progresso** no próprio aparelho (localStorage).
- **Funcionar offline** (depois de instalado / em cache).
- **Atualizar apenas reenviando o `index.html`** para o mesmo repositório (o progresso não
  precisa migrar entre ciclos).
- **Zero dependências externas**: sem CDNs, sem fontes externas, sem imagens remotas. Tudo
  inline (CSS + JS + emojis). Isso é uma **regra rígida**.

---

## 2. Estrutura de arquivos

```
/root/chat/2026-11-13/session-82f45244-5a1f-4c0b-9bc4-d9f5d86ca432/
├── README.md                  ← este arquivo
├── hybrid35-site.zip          ← zip com TODO o conteúdo de site/ (backup / envio em lote)
├── Treino-Hybrid35.html       ← versão antiga (obsoleta, mantida só como histórico)
├── icon-*.png, apple-touch-icon.png, favicon.svg, manifest.webmanifest
│                              ← cópias soltas na raiz (legado; a versão que vale é a de site/)
├── inbox/                     ← arquivos que chegam via "compartilhar" do Android (ex.: images.jpg)
└── site/                      ← ⭐ ESTA É A PASTA QUE VAI PARA O GITHUB PAGES
    ├── index.html                 ← APP PRINCIPAL (4 abas) — é o arquivo mais editado
    ├── manifest.webmanifest       ← manifest do app principal
    ├── icon-192.png               ← ícone 192
    ├── icon-512.png               ← ícone 512
    ├── icon-maskable-512.png      ← ícone maskable (Android)
    ├── apple-touch-icon.png       ← ícone iOS
    ├── favicon.svg                ← favicon
    ├── ana.html                   ← APP DA ANA (30 treinos)
    ├── ana-manifest.webmanifest   ← manifest da Ana
    ├── ana-icon-192.png           ← ícones da Ana (tema menta→coral)
    ├── ana-icon-512.png
    ├── ana-icon-maskable-512.png
    ├── ana-apple-touch-icon.png
    └── ana-favicon.svg
```

**Importante:** tudo o que é publicado fica em `site/` na **raiz do repositório** (estrutura
plana, sem subpasta) para facilitar o upload pelo celular no GitHub. O `index.html` fica na
raiz do site → a URL do app principal **não muda** ao longo do tempo.

---

## 3. Hospedagem e publicação

- **GitHub Pages**, repositório **público**. A URL é fixa (mesmo endereço a cada atualização).
  - ⚠️ **URL exata do repositório: A CONFIRMAR** (não está registrada neste projeto). Se a IA
    precisar dela, pergunte ao usuário.
- **Upload é manual pelo usuário** (pelo navegador do celular): ele reenvia os arquivos
  alterados para a raiz do repositório e espera 1–2 min.
- Não há `gh` CLI nem credenciais no ambiente. Existe a opção de fazer `git push` via
  **Personal Access Token (fine-grained, Contents: Read and write)** se o usuário fornecer —
  nesse caso, use variável de ambiente e **nunca ecoe o token**; depois avise para revogá-lo.

### Fluxo típico de atualização (o que dizer ao usuário)
1. Editar os arquivos em `site/`.
2. Validar (ver seção 8).
3. Salvar o(s) arquivo(s) alterado(s) em **Downloads** do celular via bridge (ver seção 7).
4. Usuário sobe no repositório (substituindo) e espera 1–2 min.

Na última rodada, os arquivos salvos em Downloads foram `index.html` e `manifest.webmanifest`.
Depois de qualquer mudança, **só o que mudou precisa ser reenviado** (mas reenviar tudo não faz
mal).

---

## 4. App principal — `site/index.html`

Arquivo **auto-contido** (HTML + `<style>` + `<script>` inline). ~631 linhas, ~64 KB.
`<title>Hybrid 35 + Hyrox + CrossFit + Race</title>`, tema `#111827`.

### 4.1 As 4 abas

| Aba (rótulo do botão) | Programa | Treinos | Blocos (duração) | Meta |
|---|---|---|---|---|
| `⚡ Hybrid` | Hybrid 35 | **35** | Aquecimento 5' · Força (musculação) 14' · Aeróbico (Hyrox/CrossFit) 21' | **40 min** |
| `🔥 Hyrox` | Hyrox | **10** | Aquecimento 5' · Preparação (força/técnica) 10' · Bloco Hyrox 30' | **45 min** |
| `🏋️ CrossFit` | CrossFit | **10** | Aquecimento 5' · Força/Skill 12' · Metcon 23' | **40 min** |
| `🏁 Race` | Race | **10** | Aquecimento 5' · Circuito funcional 12' · Esteira + funcional 28' | **45 min** |

Total: **65 treinos**.

### 4.2 Grupos/fases por aba (como aparecem na grade)

- **Hybrid 35:** Fase 1 · Lombar Safe (1–5) · Fase 2 · Base Híbrida (6–15) · Fase 3 · Construção
  (16–27) · Fase 4 · Performance (28–35).
- **Hyrox:** Corrida & Estações (1–5) · Simulações & Engine (6–10).
- **CrossFit:** Benchmarks (1–5) · Força + Metcon (6–10).
- **Race:** Tiros & Velocidade (1–5) · Alternado & Força (6–10).

### 4.3 Chaves de progresso (localStorage)

- `hybrid35_v6` → progresso da aba Hybrid 35 (**preservado desde a v1; NÃO trocar esta chave**).
- `hyrox_v1` → progresso da aba Hyrox.
- `crossfit_v1` → progresso da aba CrossFit.
- `race_v1` → progresso da aba Race.
- `hybrid_prog` → qual aba está selecionada (persistida entre aberturas).

Cada aba tem progresso **independente**. O **backup/exportação cobre todas as abas juntas**.

### 4.4 Modelo de dados dos treinos

Cada treino é um array de 7 campos, mapeado por `mk()`:

```
[fase, título, foco, aquecimento, força/circuito, condicionamento, nota]
```

Depois `mk()` transforma em objeto:
`{id, phase, title, focus, warmup, strength, cond, note}`.

As definições de programa ficam no objeto `PROGRAMS` (chaveado por `h35`, `hyrox`, `cf`,
`race`), cada um com: `key` (chave de storage), `emoji`, `brand`, `short`, `tab`, `meta`
(minutos), `blocks`, `groups`, `workouts`, `foot` (rodapé do programa), `guide` (texto do
"Como usar").

Arrays de dados: `data` (35 Hybrid 35 — **preservado verbatim**), `hyroxData` (10),
`cfData` (10), `raceData` (10).

### 4.5 Recursos do app principal

- Cronômetro de treino (por blocos, com a meta de minutos de cada aba).
- Marcação de treino concluído, progresso e "Próximo pendente".
- Registro de **dor (escala)** e **notas** por treino.
- Seção **"↔️ Equivalências"** (substituições de equipamento — ver seção 6).
- Seção **"📋 Como usar"** (frequência, progressão, segurança) — texto muda por aba.
- Seção **"💾 Backup do progresso"** (Exportar/Importar/Zerar esta aba).
- Banner de aviso quando o navegador não está salvando (ex.: aberto via `file://`).
- Link cruzado para o app da Ana (`<a href="ana.html">`).
- **🗓️ Sua semana** (rodapé): plano semanal fixo do usuário (ver seção 5), com o dia de hoje
  destacado automaticamente via JS (`new Date().getDay()` + `data-d` nos `<li>`).

---

## 5. Plano semanal do usuário (embutido no rodapé do index)

O usuário treina **segunda, quinta, sexta, sábado e domingo** (descansa terça e quarta).
Plano "foco Hyrox" — 3 de força + 2 de condicionamento, alternando para não empilhar dois dias
pesados de perna:

| Dia | Treino |
|---|---|
| **Seg** | ⚡ Hybrid 35 · força |
| Ter | Descanso |
| Qua | Descanso |
| **Qui** | ⚡ Hybrid 35 · força |
| **Sex** | 🏁 Race |
| **Sáb** | ⚡ Hybrid 35 · força |
| **Dom** | 🔥 Hyrox |

Observações dadas ao usuário:
- Pode trocar a **Race (Sex)** ou o **Hyrox (Dom)** por **CrossFit**.
- Enquanto estiver na **Fase 1 do Hybrid 35** (proteção da lombar), deixar o Hyrox de domingo
  mais leve ou trocar por CrossFit.

---

## 6. Adaptações de equipamento (aplicam-se a TODOS os programas)

O usuário treina em **academia comum**. Não há remo, sled, SkiErg nem wall ball. Substituições
oficiais (já embutidas nos textos dos treinos e na seção "Equivalências"):

| Original | Substituto |
|---|---|
| Remo / Assault Bike | bike ergométrica em esforço forte **ou** esteira |
| SkiErg | puxada de braços retos no cabo, ritmo rápido |
| Sled push | avanço caminhando com halteres pesados |
| Sled pull | remada baixa no cabo, carga pesada |
| Wall ball | thruster com halteres (agachamento + desenvolvimento) |
| KB swing | pode ser feito com um halter segurado com as duas mãos |
| Barra fixa | puxada alta ou remada com halter, se ainda não tiver repetições |
| RPE | esforço 1–10; RPE 7 = forte e controlado, RPE 5 = ritmo de conversa |

---

## 7. App da Ana — `site/ana.html`

- App separado, **iOS-friendly**: metas `apple-mobile-web-app-*`, `apple-touch-icon`,
  `apple-mobile-web-app-title = "30 Treinos"`.
- **30 treinos** em **3 fases** (Musculação / Funcional / HIIT), chamados via função `W(...)`.
- Filtros por fase e por tipo.
- Tema **menta → coral** (`--mint:#4fe3b5`, `--coral:#ff7b6b`), fundo `#141b26`.
- Chave de storage: **`treinos30_v2`** (migra automaticamente de `treinos30_v1`).
- Link cruzado de volta ao app principal: `<a class="cross" href="./">`.
- Manifest: `ana-manifest.webmanifest` (`id`/`start_url`/`scope` = `ana.html`).
- Fonte do programa: `/root/chat/inbox/treino ana.html` (já incorporado no `ana.html`).
- ⚠️ **Nunca** usar `href="./"` como start_url no manifest da Ana — ela precisa abrir o app dela.

---

## 8. Validação / testes (rodar SEMPRE antes de publicar)

O ambiente tem Node v22 e Python 3.12.

**1) Checar sintaxe do JS embutido e ausência de recursos externos:**

```bash
cd site
python3 - <<'PY'
import re
s=open('index.html',encoding='utf-8').read()
sc=re.findall(r'<script[^>]*>(.*?)</script>',s,re.S)
open('/tmp/opencode/index.js','w',encoding='utf-8').write('\n;\n'.join(sc))
PY
node --check /tmp/opencode/index.js && echo "JS OK"
grep -aoE '(src|href)="https?://[^"]*"' index.html || echo "nenhum externo"   # deve dar "nenhum externo"
grep -acE '^\[[0-9]+,"' index.html   # deve dar 65
```

**2) Harness DOM-stub (`/tmp/opencode/test.js`)** — avalia o JS do app com `document`/`window`/
`localStorage` falsos e confere contagem de treinos, blocos e troca de abas. Rodar:

```bash
node /tmp/opencode/test.js
```

Saída esperada: `h35 35 / hyrox 10 / cf 10 / race 10`, soma de blocos igual à meta de cada aba
e `render + troca de abas: OK`.

> O stub já inclui `document.querySelector: ()=>mkEl()` (necessário por causa do destaque do dia
> na semana). Se o app passar a usar outros métodos do DOM, adicione-os ao stub.

**3) Checar PNGs e zips** quando mexer em ícones:
- `python3` + Pillow para validar dimensões dos PNGs.
- `unzip -l hybrid35-site.zip` para conferir o conteúdo do zip.

---

## 9. Ambiente / ferramentas do sandbox

- **Diretório de trabalho (mudou!):** `/root/chat/2026-11-13/session-82f45244-5a1f-4c0b-9bc4-d9f5d86ca432`.
  Os caminhos do bridge Android são **relativos a ele** (use `site/index.html`, NÃO o prefixo
  antigo `2026-11-13/session-.../site/...`, que agora retorna `not_a_file`).
- **Bridge Android:** `python3 "$OPENCODE_CONFIG_HOME/skills/android-device/android_device.py"`
  - `file-save --path <p> --destination downloads [--name <n>]` → salva no Downloads do celular.
    **Use sempre `--name`** para evitar o auto-rename de duplicados do Android.
  - `file-list`, `file-pick` (instável), etc. **Não há ação de apagar.**
- **Git:** 2.43.0 · **curl** disponível · **sem `gh`** · **sem ImageMagick**.
- **Python 3.12 + Pillow** (gerar/validar PNGs).
- **`apt-get` funciona** (mirrors aliyun). Já instalado: `tesseract-ocr` + `tesseract-ocr-por`
  (v5.3.4) — usado como **OCR** porque **o modelo não vê imagens**.
- **Preferir `/tmp/opencode`** para arquivos temporários.

### Limitações importantes do agente
- **Não é possível visualizar imagens** (o modelo não tem entrada de imagem). Para ler um print,
  usar OCR via `tesseract`. Imagens coladas no chat **não** chegam ao filesystem; arquivos
  compartilhados do Android caem em `inbox/`.
- `file-pick` é instável (`canceled` / `invalid_mime`).

---

## 10. Decisões e regras que NÃO podem ser quebradas

1. **Auto-contido:** nada de CDN, fonte externa, script externo ou imagem remota. Emojis para
   ícones. (Validar com o grep de "externos".)
2. **`hybrid35_v6`** é a chave do Hybrid 35 — **não renomear** (preserva progresso do usuário).
3. **`site/index.html`** é o arquivo que o usuário envia; manter na **raiz** para a URL não mudar.
4. Estrutura **plana** em `site/` (sem subpastas) para facilitar upload pelo celular.
5. App da Ana com prefixo **`ana-`** nos arquivos, no mesmo nível.
6. Os **35 treinos do Hybrid 35** ficam **verbatim** no array `data` (não reescrever sem pedido).
7. **Adaptações de equipamento** da seção 6 valem para qualquer treino novo.
8. Treinos novos: **40 ou 45 min**, academia comum, formato `[fase, título, foco, aquecimento,
   força, cond, nota]`.
9. **Proteção da lombar** nas 5 primeiras sessões do Hybrid 35 (Fase 1) — não sugerir Hyrox
   pesado nessa fase.

---

## 11. Histórico do que já foi feito

1. App Hybrid 35 original (35 treinos, 4 fases) — reescrito **auto-contido** e publicado. ✅
2. Ícones + manifest do Hybrid 35 gerados (`/tmp/opencode/genicon.js`). ✅
3. App da **Ana** (30 treinos, tema menta/coral, metas iOS, cross-links) + ícones
   (`/tmp/opencode/genicon2.js`). ✅
4. `index.html` reescrito em **3 abas** (Hybrid 35 / Hyrox / CrossFit). ✅
5. **4ª aba Race** adicionada (10 treinos). ✅
6. Race **reescrita** para se diferenciar do Hyrox: agora é **esteira com velocidade variável**
   (tiro, progressivo, sobe-desce) **alternando esteira ↔ funcional**, em vez de distância fixa
   + estação (que é o Hyrox). ✅
7. **🗓️ Sua semana** adicionado ao rodapé, com destaque do dia atual. ✅

### Diferença conceitual Hyrox × Race (importante para treinos futuros)
- **Hyrox** = **distância fixa** (200/400/800 m/1 km) + **estação** — simula a prova.
- **Race** (estilo Race Bootcamp) = **tempo/velocidade na esteira** mudando o tempo todo,
  **alternando** esteira ↔ funcional várias vezes, ~45 min, estilo de estúdio.

---

## 12. Próximos passos possíveis (backlog)

- Confirmar/registrar a **URL do repositório GitHub Pages** neste README.
- Opcional: automatizar o push via PAT (env var, nunca ecoar o token).
- Ajustar o plano semanal se o usuário quiser (ex.: CrossFit fixo no lugar da Race).
- Trocar/ajustar treinos de qualquer aba conforme feedback.
- Se o usuário quiser, gerar variações de treino (novos ciclos) mantendo as mesmas chaves de
  storage (ou subindo a versão da chave quando fizer sentido).

---

## 13. Checklist rápido antes de publicar

- [ ] `node --check` no JS → OK
- [ ] grep de externos → "nenhum"
- [ ] contagem de treinos → 65 (index) / 30 (ana)
- [ ] `node /tmp/opencode/test.js` → tudo OK
- [ ] salvar arquivo(s) alterado(s) em **Downloads** via bridge (com `--name`)
- [ ] avisar o usuário de **quais arquivos** subir e esperar 1–2 min
