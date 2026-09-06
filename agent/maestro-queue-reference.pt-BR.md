# `_director_queue.json` do Maestro — Referência de Schema e Guia Prático

> **Licença.** © 2026 **Ieso Nagata (iesonagata [at] gmail [dot] com)** — licenciado sob
> **CC BY 4.0** (https://creativecommons.org/licenses/by/4.0/). Compartilhe e adapte
> livremente com atribuição. Veja [`LICENSE`](LICENSE).

> **Propósito.** Documento-skill para o director do opencode — dois papéis em um: (1)
> **referência operacional** para ler o estado vivo da fila que o **Maestro** grava ao
> renderizar um videoclipe com **MiniMax H3 ref2va** (acompanhar progresso, prever tempos de
> render, auditar prompts, mapear MP4s aos clips); (2) **fonte de corpus criativo + técnico**
> para o prompt-director — minerar uma fila como verdade de referência para estrutura,
> invariantes e configurações de inferência (§9).
>
> **Genercidade.** Este schema é independente de hardware. Os valores de exemplo
> ("valor observado") vêm de uma execução de produção no workstation do autor — trate-os
> como ilustração, jamais como especificação de desempenho. As orientações de tempo são
> dadas como *regras de escala*, não números absolutos.
>
> **Fonte.** O formato `_director_queue.json` documentado aqui é a fila viva de
> [`Blizaine/Maestro`](https://github.com/Blizaine/Maestro) (WanGP Non-Commercial
> Evaluation License 1.1; upstream `deepbeepmeep/Wan2GP`). Este documento descreve o
> schema como referência factual e não redistribui código nem pesos do Maestro.
>
> **Data:** 06/09/2026.

---

## 1. O que este arquivo é (e o que não é)

`_director_queue.json` é o **arquivo de estado vivo** que o Maestro grava em disco durante
o trabalho:

- Caminho: `app/outputs/_director_queue.json` (workspace do Maestro).
- **Não** é um workflow ComfyUI, **não** é consumível por `run_workflow`/`run_template` do
  comfy-mcp. O Maestro renderiza nativamente via `app/models/minimax_h3/ref2va.py` e os
  serviços de direction em `app/services/`.
- Seu conteúdo **muda durante o render** (`status`, `message`, timestamps). Leia como
  snapshot, não como documento estático.
- O prompt compilado de cada clip vive em `prepared_clip_plans[]._director_h3_compiled_prompt`
  — o prompt real enviado ao modelo e o campo mais útil para auditar o que o modelo recebeu.

---

## 2. Estrutura de nível superior

```json
{
  "version": 1,
  "paused": false,
  "running": true,
  "entries": [ { ... } ]
}
```

| Campo | Tipo | Significado |
|-------|------|-------------|
| `version` | int | Versão do formato (atual: 1). |
| `paused` | bool | Fila pausada (ação do operador). |
| `running` | bool | Se a fila está processando. |
| `entries` | array | Trabalhos em lote na fila. Uma entrada carrega o plano completo de 45 clips. |

---

## 3. Entry — campos de nível do trabalho

| Campo | Significado |
|-------|-------------|
| `id` | Id da entrada (também nomeia o diretório de assets em `_director_queue_assets/<id>/`). |
| `status` | `pending` / `running` / `completed` / `error`. |
| `message` | **Progresso vivo.** Ex.: `"Clip 22/45: Prompt 22/45 - Denoising \| 1m 11s"` — índice/total do clip atual, estágio (Encoding Prompt / Denoising / ...) e tempo decorrido. |
| `created_at` / `started_at` / `completed_at` | Timestamps Unix (float). |
| `pipeline_id` | Id do pipeline; `_director_parent_pipeline_id` aponta para a entrada do director que o originou. |
| `error` | Snapshot de erro em falha, senão `null`. |
| `params` | O estado completo das decisões do director (abaixo). |

---

## 4. `params` — registro completo das decisões do director

### 4.1 Identidade e pipeline

| Campo | Valor observado | Significado |
|-------|-----------------|-------------|
| `pipeline_type` | `music_video` | Pipeline que conduz o render. |
| `auto_mode` | `true` | Director rodou sem edições manuais por clip. |
| `_director_project_id` | `1916f9b3` | Projeto director de origem. |
| `use_director_v2` | `true` | Usa o caminho de planejamento V2 (fluxo de 48 etapas do director). |
| `seamless` | `false` | Sem mesclagem seamless entre clips. |
| `shot_image_guidance` | `auto` | Política de shot image guidance. |

### 4.2 Música e LLM

| Campo | Valor observado | Significado |
|-------|-----------------|-------------|
| `scene_description` | texto | Briefing do usuário para o vídeo inteiro. |
| `bpm` | `161.5` | Tempo da faixa; dirige a segmentação `beat_count`/energia. |
| `lyrics` | array (22) | Seções/linhas de letra usadas para lipsync e estrutura de versos. |
| `target_duration` | `30` | Duração-alvo do clip em segundos (orçamento por clip, não total). |
| `llm_model_id` | qualquer LLM GGUF (ex.: `my-llm-GGUF`) | LLM que planejou os clips, rodando localmente. |
| `llm_device` | `cpu` | Onde esse LLM rodou. |

### 4.3 Escolha de modelos

| Campo | Valor observado | Significado |
|-------|-----------------|-------------|
| `image_model` | `flux2_klein_9b` | Modelo para as imagens de shot/preview (`image_params`). |
| `video_model` | `minimax_h3_ref2va` | O modelo de vídeo que efetivamente renderiza. |
| `director_resolution_preset` | `540p` | Tier escolhido. |
| `director_aspect_ratio` | `16:9` | Proporção. |
| `fps` | `24` | Quadros por segundo. |
| `director_h3_reference_detail` | `max` | Nível de condicionamento por referência (equivale ao `minimax_h3_reference_detail` do workflow). |

### 4.4 `video_params` — as configurações efetivas de inferência

| Campo | Valor observado | Significado |
|-------|-----------------|-------------|
| `resolution` | `960x544` | Tamanho renderizado. |
| `num_inference_steps` | `8` | Passos de denoising (constante no lote). |
| `video_length` | `124` | Comprimento-base do clip em frames (`frames_minimum`). |
| `sliding_window_size` | `345` | Janela de contexto do passo de atenção. |
| `sliding_window_overlap` | `18` | Sobreposição de frames entre janelas. |
| `override_attention` | `sol` | `sol` = atenção linear suavizada (o `sol` das linhas de log). |
| `minimax_h3_turbo_mode` | `true` | Preset rápido ativado. |
| `minimax_h3_turbo_preset` | `alibaba-pai-ref2va-pdd-8step` | O preset turbo exato (PDD 8 passos). |
| `skip_steps_multiplier` | `0.08` | Cache de passos destilados a partir dessa fração do total. |
| `minimax_h3_reference_detail` | `match` | Override do nível de referência por clip. |

### 4.5 `prepared_clip_plans` vs `planned_clips` (ambos com 45)

| Array | Conteúdo |
|-------|----------|
| `planned_clips` | O **plano criativo** — por clip: timing, seção, energia, sujeitos, blocking, plano de áudio, prompt-fonte. |
| `prepared_clip_plans` | O **plano pronto para render** — `prepared_clip_plans[i]` = `planned_clips[i]` compilado em um `video_prompt`/`_director_h3_compiled_prompt` final, mais o contrato vocal e as âncoras de contexto. |
| `prepared_planned_clips` | Espelho do plano preparado (mesmo tamanho, mesma ordem). |

Regra prática: `planned_clips[i]` diz *o que o director quis*; `prepared_clip_plans[i]`
diz *o que o modelo realmente recebeu*.

---

## 5. `planned_clips` — schema do clip

Cada clip `i` bate exatamente com `prepared_clip_plans[i]`.

| Campo | Tipo | Significado |
|-------|------|-------------|
| `start` / `end` | int | Faixa de beats na música (beats). |
| `beat_count` | int | Beats cobertos por este clip. |
| `section_label` | str | `intro` / `verse` / `bridge` / `chorus` / `outro`. |
| `energy` | float | Energia normalizada 0–1 (dirige o ritmo; `suggested_prompt_hint` resume). |
| `suggested_prompt_hint` | str | Ex.: `"intro, low energy"`. |
| `duration_frames` | int | Contagem de frames. **Define o custo do render**: `243` ≈ 10,1 s, `192` ≈ 8 s, `175` ≈ 7,3 s, `226` ≈ 9,4 s, `141` ≈ 5,9 s. |
| `duration_sec` | int | Segundos (arredonda de frames @ 24 fps). |
| `dominant_speaker` | str \| null | Id do falante quando o clip tem diálogo. |
| `_director_segment_index` / `_director_segment_count` | int | Segmento da música a que o clip pertence. |
| `_director_source_clip_indices` | array | Clip(s) de origem quando este foi fatiado de um plano maior. |
| `_director_continuity_strategy` | str | `independent` (cada clip autocontido, sem chain frame) — valor observado; outros podem surgir em runs encadeados. |
| `_director_continuity_group` | str | Id do grupo de continuidade quando os clips se encadeiam; `''` quando independente. |
| `_director_opening_blocking` / `_director_closing_blocking` | str | Estado de câmera/ação no início e fim do clip. |
| `_director_dialogue_beats` | array | Beats de diálogo/vocal cronometrados do clip. |
| `_director_subjects_on_screen` | array | `{visual_description, position_or_relation}` por sujeito em cena. |
| `_director_h3_source_prompt` | str | O prompt narrativo do director para o clip. |
| `_director_h3_prompt_mode` / `_director_h3_model_family` | str | Ambos `ref2va` aqui. |
| `_director_project_context` | str | Invariantes entre clips (identidade do personagem, modelo do microfone, figurino). |
| `_director_environment` | str | Ambiente persistente reafirmado por clip. |
| `_director_audio_plan` | obj | `{mode: "music_driven", timing_anchor: "audio", lip_sync_critical: bool}`. |

---

## 6. `prepared_clip_plans[i]` — prompt compilado, pronto para render

Campos extras além do conjunto de `planned_clips[i]`:

| Campo | Significado |
|-------|-------------|
| `image_prompt` | Prompt da imagem de shot (`''` quando ref2va usa as imagens de referência). |
| `video_prompt` | Prompt final montado: bloco `subject_definitions:` + ação + câmera/público. |
| `visual_changes` | Array de deltas visuais por tempo para o clip. |
| `keyframe_prompts` / `window_prompts` / `window_count` | Quebra multi-janela quando o clip excede a janela do modelo. |
| `image_source` | `original` = usa diretamente as referências enviadas. |
| `_director_vocal_contract` | Regra fixa: qualquer voz audível vem somente do áudio dirigente (sem gibberish gerado). |
| `_director_h3_compiled_prompt` | **A string exata do prompt enviado ao modelo** — o campo a auditar. |
| `_director_speaker_registry` | Falantes mapeados (vazio quando não há diálogo mapeado). |
| `_director_required_context_anchors` | Âncoras de referência exigidas pelo clip. |

---

## 7. `_director_asset_manifest` — os insumos

Em `params._director_asset_manifest`:

| Campo | Significado |
|-------|-------------|
| `audio_path` | `{path, serve_path, original_name}` — mix completo em WAV (aqui 48 kHz estéreo, ~385 s). |
| `audio_vocals_path` | Vocais isolados em WAV (mesmo comprimento, 44,1 kHz) usado para sync lipsync/music_driven. |
| `minimax_h3_references` | As referências de imagem/vídeo (os sujeitos, o ambiente) e seus rótulos. |

Os arquivos ficam em `app/outputs/_director_queue_assets/<entry_id>/`.

---

## 8. Guia prático — lendo o render ao vivo

### 8.1 Acompanhar o progresso

- `entry.message` é o cursor vivo: `"Clip 22/45: Prompt 22/45 - Denoising | 1m 11s"`.
- Renderizados: `app/outputs/*.mp4`, nomeados `YYYY-MM-DD-HHhMMmSSs_seed<seed>_subject_definitions....mp4`.
  Ordenar por nome = ordem de conclusão; os **timestamps são o relógio local do término**.
- **Clips concluídos = contagem de `.mp4`** no diretório de outputs (um MP4 por clip).

### 8.2 Prever tempo de render

Os custos pesados, por clip `i`:

1. **Denoising** ≈ `num_inference_steps × s/passo`. `s/passo` escala com `duration_frames`
   (packed rows por latente): um clip de 243 frames custa ~20–80 % a mais por passo que um
   de 192 frames, dependendo da resolução e da precisão do modelo. A velocidade de geração da
   GPU (VRAM, clocks) e a quantização do modelo dominam o número absoluto.
2. **Pós-processamento** (VAE decode completo + áudio + mux): poucos minutos
   aproximadamente constantes por clip, aproximadamente proporcional à contagem de frames.

A *proporção* por contagem de frames é a parte portável: meça um clip no seu próprio hardware
e escale os restantes por `duration_frames` para um ETA grosseiro. Some
`duration_frames`/`duration_sec` dos clips restantes para uma primeira estimativa.

### 8.3 Auditar o que o modelo recebeu

Para qualquer clip, leia `prepared_clip_plans[i]._director_h3_compiled_prompt` e confira
contra o `validator.md`:

- **Continuidade**: `_director_continuity_strategy: independent` significa clip autocontido —
  o prompt compilado precisa reafirmar ambiente + identidade do sujeito a cada clip
  (`_director_environment`, `_director_project_context`, `subject_definitions:`), o que este
  pipeline faz.
- **Lipsync**: `_director_audio_plan.mode = music_driven` + `_director_vocal_contract` — as
  vozes devem vir apenas de `audio_vocals_path`.
- **Disposição dos sujeitos**: `_director_subjects_on_screen` declara posições; opening/closing
  blocking declaram a continuidade do movimento nos cortes.

### 8.4 Checagens de sanidade

- `planned_clips` e `prepared_clip_plans` devem ter ambos 45 itens, na mesma ordem.
- `duration_frames` deve cair na grade de frames do modelo; `frames_minimum` (124) é o piso.
- `energy` deve seguir o formato da música (low no intro/outro, picos no chorus) — um array
  `energy` chapado com `section_label` idêntico em tudo sugere que o planejador colapsou.
- Soma de `duration_sec` ≈ duração da música (~384,8 s nos 45 clips aqui).

---

## 9. Usar a fila como fonte criativa + técnica para a geração

Além de acompanhar um render ao vivo, a fila é um **corpus de referência real**: contém o
registro completo das decisões de um passe do director — o que ele planejou (`planned_clips`),
o que compilou em prompts efetivos (`prepared_clip_plans`) e quais configurações de inferência
usou. Isso a torna entrada de primeira classe no fluxo do *prompt-director*, não só um log de
operações.

### 9.1 O que minerar e por quê

| Campo-fonte | Valor criativo | Use como... |
|-------------|----------------|-------------|
| `prepared_clip_plans[i]._director_h3_compiled_prompt` | O **prompt exato que o modelo executou** — estrutura, redação de subject_definitions, linguagem de câmera/público, ritmo. | A referência de como é um prompt complexo de clip "funcionando"; copie a *estrutura*, não o conteúdo. |
| `planned_clips[i]._director_subjects_on_screen` | O idioma declarativo `visual_description` + `position_or_relation` para posicionar múltiplos sujeitos. | Template de composição multi-sujeito nas suas cenas. |
| `planned_clips[i]._director_environment` | Como um ambiente persistente é reafirmado por clip. | O padrão para manter um local estável entre cortes. |
| `planned_clips[i]._director_project_context` | Invariantes entre clips (identidade, adereços, figurino) carregados verbatim. | Checklist do que *seus* prompts devem reafirmar a cada cena. |
| `planned_clips[i]._director_opening_blocking` / `_director_closing_blocking` | Continuidade de câmera/ação declarada nos pontos exatos de corte. | O modelo para escrever hard cuts sem costura. |
| `planned_clips[i]._director_audio_plan` + `_director_vocal_contract` | A regra de sync music_driven e o contrato "sem gibberish gerado". | Boilerplate para qualquer clip music-driven / lipsync. |
| `planned_clips[i].section_label` + `.energy` | O vocabulário de estrutura de música (intro/verse/bridge/chorus/outro) com energia 0–1. | Vocabulário de ritmo; re-mapeie `energy` para o formato da sua faixa. |
| `video_params` (passos, `override_attention`, preset turbo, resolução) | Configurações de inferência que funcionaram no ref2va 8-step. | Defaults técnicos para runs equivalentes seus. |
| `duration_frames` | Quanto cada tipo de seção de fato renderizou. | Piso/teto de sanidade no planejamento de duração de clip. |

### 9.2 Os três modos de colheita

1. **Estrutural** — leia 3–5 prompts compilados de *seções diferentes* (um `intro`, um `verse`,
   um `chorus`) para absorver como o director organiza subject_definitions → ação → câmera →
   público em cada tier de energia. Depois escreva seu prompt seguindo esse esqueleto.
2. **Reafirmação** — reutilize `_director_project_context` / `_director_environment` /
   `_director_vocal_contract` quase verbatim: são invariantes copy-paste-safes que valem para a
   fila inteira e seguem válidos no seu projeto trocando seus próprios sujeitos.
3. **Calibração** — para uma música *nova*, colete os tuples `energy`/`section_label`/
   `duration_frame` de uma fila music_video anterior e peça ao prompt-director para espelhar
   esse ritmo. Um array `energy` chapado (todos os clips com o mesmo valor) é bandeira para
   sobrepor.

### 9.3 Como plugar no fluxo do prompt-director

```
fila (corpus de referência)
  → prompt-director.md gera sua cena, tomando estrutura + invariantes do §9.1
  → validator.md confere continuidade (idiomas do §8.3)
  → render
```

Carregue `maestro-queue-reference.md` *antes* da entrevista quando o briefing incluir algo de:
"renderizar um videoclipe / lote ref2va / fila de 45 clips", ou quando você tiver uma fila do
Maestro de um run anterior para minerar. Aponte o director para o caminho da fila e para os
índices de clip que melhor ilustram o tipo de seção que você vai escrever.

---

## 10. Pegadinhas

- **Não edite** `_director_queue.json` com `running: true` — o Maestro é dono do arquivo.
- O JSON da fila **não** guarda o MP4 renderizado; os outputs saem em `app/outputs/` e são
  vinculados ao clip apenas por ordem/nome, sem um campo explícito `clip_index`.
- `video_params.video_length` (124) é o comprimento **base**; o comprimento por clip é
  `planned_clips[i].duration_frames` e pode excedê-lo (até 243).
- O `comfy-mcp` não renderiza esse plano diretamente. Para renderizar fora do Maestro,
  traduza um prompt compilado (`prepared_clip_plans[i]._director_h3_compiled_prompt`) nos
  slots do workflow `muse_minimax_h3_director_V1.4.json`.
- A fila é **um passe do director sobre uma música**: copie a *estrutura e os invariantes*,
  nunca o *conteúdo* dos clips (sujeitos/ambiente/letra pertencem a esse projeto).