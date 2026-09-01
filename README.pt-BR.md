# MiniMax H3 opencode Prompt Director

![Licença: CC BY 4.0](https://img.shields.io/badge/licen%C3%A7a-CC%20BY%204.0-green.svg)
![Modelo: MiniMax H3](https://img.shields.io/badge/modelo-MiniMax%20H3-7c6cff.svg)
![Modos: T2VA · I2VA · FL2VA · L2VA · Full-ref](https://img.shields.io/badge/modos-T2VA%20·%20I2VA%20·%20FL2VA%20·%20L2VA%20·%20Full--ref-00d4b0.svg)
![Status: validado em produção](https://img.shields.io/badge/status-validado%20em%20produ%C3%A7%C3%A3o-ffd166.svg)
![Docs: EN + PT-BR](https://img.shields.io/badge/docs-EN%20%2B%20PT--BR-ff6b6b.svg)

Um kit de ferramentas universal e validado em produção para criar prompts de vídeo
tecnicamente corretos para **MiniMax H3** (T2VA / I2VA / FL2VA / L2VA e
**full-reference**), construído em torno de um fluxo de trabalho **opencode** de
**cenas encadeadas** com continuidade física.

> **Licença:** © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** — **CC BY 4.0**.
> Compartilhe e adapte livremente com atribuição. Veja [`LICENSE`](LICENSE).

## Índice

- [O que contém](#o-que-contém)
- [Início rápido](#início-rápido)
- [Exemplos](#exemplos)
- [O fluxo de trabalho encadeado (ideia central)](#o-fluxo-de-trabalho-encadeado-ideia-central)
- [Uso sugerido](#uso-sugerido)
- [Procedência](#procedência)
- [Fontes oficiais MiniMax H3 referenciadas](#fontes-oficiais-minimax-h3-referenciadas)
- [Idiomas](#idiomas)
- [Recursos](#recursos)

## O que contém

```
.
├── agent/                       # os sistemas que montam prompts (o produto central)
│   ├── prompt-director.md       #   assistente interativo (v6.1)
│   ├── validator.md             #   validador de consistência  (+ validator.pt-BR.md)
│   └── craft/                   #   camadas de craft opcionais (carregar sob demanda)
│       ├── dramaturgy.md
│       ├── creative-enhancement.md
│       └── full-reference-advanced.md
├── docs/                        # passo a passo (EN + PT)
│   ├── INSTRUCTIONS.md
│   └── INSTRUCTIONS.pt-BR.md
├── examples/                    # prompts de cena de exemplo para copiar (EN + PT)
├── presentations/               # decks de apresentação visual (EN + PT)
├── workflows/                   # workflows ComfyUI executáveis (referências removidas)
│   └── minimax-h3-full-reference.scene.json
├── index.html                   # entrada de redirecionamento (GitHub Pages)
├── README.md                    # esta página (EN)
├── README.pt-BR.md              # esta página (PT)
└── LICENSE
```

| Arquivo | Propósito |
|---------|-----------|
| `agent/prompt-director.md` | **Director de prompts v6.1.** Um assistente interativo que monta o prompt MiniMax H3 dinamicamente, incorporando parâmetros (`[PARAM]`), lições (`[LESSON]`) e a camada de craft de detalhe de produção (timed audio beats, física de macro/impacto, bokeh-como-moldura, dois pontos de temperatura de cor). Entrevista no idioma do usuário, oferece exemplos possíveis, aceita um arquivo de trama completo por caminho e gera um prompt pronto para enviar. |
| `agent/validator.md` | Um **guia genérico de validação** + checklist. Aplica-se a qualquer trama/roteiro. Cobre modos, linha de abertura, campos, shots, câmera, transições, seções full-reference, falantes, silhueta vs. rosto, texto em cena, continuidade física, os checks de craft de detalhe de produção e um checklist de qualidade por cena. PT: `agent/validator.pt-BR.md`. |
| `agent/craft/dramaturgy.md` | A **camada de craft cinematográfico** (porta do `smixs/visual-skills` dramaturgy, CC BY 4.0): fórmula de cena, lei dos três detalhes, regra dos três trabalhos, Regra dos Seis de Murch, blocking/staging/câmera/ambiente, storyboard de três camadas, ritmo, cinco âncoras — mapeada na sintaxe MiniMax H3 (REF2VA/T2VA) e reconciliada com as regras de continuidade deste repositório. |
| `agent/craft/full-reference-advanced.md` | A **camada avançada de full-reference** (derivada de `Square-Zero-Labs/video-prompting-skill`, Apache 2.0): limites de tipo de referência (`<Subject>`/`<Picture>`/`<Video>`/`<Audio>`), relação movimento-vs-câmera, transferência exata de performance facial multi-intérprete, transferência entre estilos, trailers de motion-graphics, semântica de áudio/silêncio, preparação de assets e auditoria de conformidade de rótulos. |
| `agent/craft/creative-enhancement.md` | A **camada de aprimoramento criativo** (derivada de `benjiyaya/Minimax-H3-Prompt-AgentSkill`, MIT): classificação de modo, identidade de câmera (incl. imperfeições), textura visual, arco de ritmo, detalhe de personagem + assinatura visual, geografia espacial, progressão de continuidade, design de som, per-shot quality bar, padrões de storyboard e pitfalls comuns. |
| `docs/INSTRUCTIONS.pt-BR.md` | Uso passo a passo — carregar o assistant, fornecer a trama, definir duração &amp; lote por VRAM, gerar, validar, renderizar uma cena por vez (+ solução de problemas). EN: `docs/INSTRUCTIONS.md`. |
| `presentations/presentation.pt-BR.html` | Um deck de apresentação visual (navegador) do projeto — como funciona, modos, lotes por VRAM, continuidade. EN: `presentations/presentation.en.html`. |
| `examples/` | Prompts de cena de exemplo para copiar (EN + PT). |
| `workflows/minimax-h3-full-reference.scene.json` | Um **workflow ComfyUI** (com referências removidas) usado para testes de cena em full-reference (REF2VA) — o campo `PROMPT` foi esvaziado e as imagens de referência carregadas foram limpas, sem nenhum dado do usuário. Use com o prompt gerado pelo assistant para a sua cena. |
| `README.md` | Versão em inglês desta página. |
| `LICENSE` | Licença CC BY 4.0 e aviso de atribuição. |

## Início rápido

1. Copie [`agent/prompt-director.md`](agent/prompt-director.md) para o
   seu agente como prompt de sistema e responda à entrevista.
2. Forneça a trama (inline ou por arquivo), escolha a duração e confirme a divisão
   em lotes por VRAM.
3. Após gerar, peça ao seu agente para rodar o
   [`agent/validator.md`](agent/validator.md)
   no diretório onde estão seus prompts, antes de renderizar.

Veja [`docs/INSTRUCTIONS.pt-BR.md`](docs/INSTRUCTIONS.pt-BR.md) para o passo a passo completo.

## Exemplos

Prompts de cena prontos para copiar ficam em [`examples/`](examples/):

- `examples/cena_03_prompt.example.pt-BR.txt` — uma cena I2VA encadeada (português).
- `examples/cena_03_prompt.example.en.txt` — a mesma cena (inglês).
- `examples/cena_prod_detail.example.pt-BR.txt` — uma cena macro T2VA de detalhe de
  produção demonstrando a camada de craft v6.1: timed sound beats, física de
  macro/impacto, bokeh-como-moldura e separação de dois pontos de temperatura de cor.
- `examples/cena_prod_detail.example.en.txt` — a mesma cena (inglês).

## O fluxo de trabalho encadeado (ideia central)

1. Divida a trama em **cenas** (ex.: 12–17 cenas, ~12 s cada, 16:9, 0.7 MP a 24 fps
   como ponto de partida).
2. Para cada cena, **exporte o quadro-final** — ele vira o **chain frame**
   (`<Picture 2>`) de referência da cena seguinte.
3. Monte o prompt com o **assistant**, valide com o **validator**, depois renderize
   uma cena por vez.

> **Nota sobre MP/resolução.** A escada de megapixels/resolução (ex.: `0.7 MP · 1152×640`)
> que aparece nas cenas é **apenas referência sugerida** — a mesma escada aparece em
> templates baixados (Civitai, HuggingFace, tutoriais, YouTube) com padrões variados.
> Quem decide o tier é o usuário no questionário, conforme preferência. Ele importa
> **apenas quando o workflow é enviado ao queue via comfy-mcp** (é o tamanho solicitado
> na execução em fila); no copy/paste manual no ComfyUI, a resolução é setada no nó
> dedicado do próprio workflow, não pelo cabeçalho da cena.

## Uso sugerido

```
assistant (gera o prompt de uma cena)  →  validator (confere)  →  renderizar
```

## Procedência

As regras e a autoria foram validadas em produção no curta **"O Fio de Ariadne"**.
Os documentos foram refinados ao longo de horas de interação entre múltiplos
agentes de IA — **opencode**, **Gemini**, **ChatGPT** e **Kilo-bin** — sob curadoria
de **Ieso Nagata (iesonagata [at] gmail [dot] com)**.

**Assista ao filme** (gerado de ponta a ponta com este kit):

<sub>Nota: este é o **teste inicial** — o enredo não foi escrito em detalhe, o que gerou
inconsistências de cenas/continuidade antes dos aprimoramentos adicionados posteriormente
a este kit (dramaturgia, supervisor de continuidade, aprimoramento criativo, referência
completa avançada).</sub>

[![O Fio de Ariadne — curta com MiniMax H3](https://img.youtube.com/vi/XgA_2ZJTqR4/0.jpg)](https://www.youtube.com/watch?v=XgA_2ZJTqR4)

## Fontes oficiais MiniMax H3 referenciadas

- `docs/VIDEO_PROMPT_WRITING_GUIDE_base_en.md` — T2VA / I2VA / FL2VA / L2VA
- `docs/VIDEO_PROMPT_WRITING_GUIDE_ref_en.md` — full-reference (rewrite)

## Idiomas

Este projeto está disponível em **inglês** e **português (Brasil)**:

- **English:** `README.md`, `docs/INSTRUCTIONS.md`, `agent/validator.md`,
  `examples/*.en.txt`, `presentations/presentation.en.html`
- **Português (BR):** `README.pt-BR.md`, `docs/INSTRUCTIONS.pt-BR.md`,
  `agent/validator.pt-BR.md`, `examples/*.pt-BR.txt`,
  `presentations/presentation.pt-BR.html`, e o `agent/prompt-director.md` que entrevista você
  no seu próprio idioma.

## Recursos

Repositórios de skills cujo conteúdo foi adaptado a este kit (com atribuição mantida):

- [`smixs/visual-skills`](https://github.com/smixs/visual-skills) — **CC BY 4.0**
  fonte de `agent/craft/dramaturgy.md` (fórmula de cena, Regra dos Seis de Murch,
  bloqueio/staging, ritmo, cinco âncoras).
- [`Square-Zero-Labs/video-prompting-skill`](https://github.com/Square-Zero-Labs/video-prompting-skill) — **Apache 2.0**
  fonte de `agent/craft/full-reference-advanced.md` (limites de tipo de referência,
  transferência facial multi-intérprete, motion-graphics, semântica de áudio).
- [`benjiyaya/Minimax-H3-Prompt-AgentSkill`](https://github.com/benjiyaya/Minimax-H3-Prompt-AgentSkill) — **MIT**
  fonte de `agent/craft/creative-enhancement.md` (sete dimensões criativas,
  per-shot quality bar, storytelling/action patterns, pitfalls).

---

*Versão em inglês: [`README.md`](README.md)*
