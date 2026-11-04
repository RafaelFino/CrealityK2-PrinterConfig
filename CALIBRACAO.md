# Guia de Calibração — Creality K2 + extrusora DXC2

Guia operacional para diagnosticar e corrigir **sub-extrusão** e calibrar o fluxo
na ordem correta. Setup real: **Creality K2 (Klipper) + extrusora DXC2**. Configs em
`default/` (stock) e `DXC2/` (atual).

> ## ✅ RESOLVIDO (nov/2026)
>
> **Causa real:** combinação de **(1) tensão do idler da extrusora mal regulada** e
> **(2) filamento inadequado** (não era um filamento premium de verdade). Juntos,
> causaram sub-extrusão, patinagem e pó de filamento.
>
> **Correção:** trocar para um **filamento premium de verdade**, **regular a tensão
> do idler** e repetir a calibração da extrusora várias vezes até convergir.
> **`rotation_distance` final: `6.86`.** Resultado: **impressão perfeita**.
>
> **Lições:**
> - Antes de mexer em `rotation_distance`, temperatura, MVS ou flow ratio, **elimine
>   as causas básicas**: tensão do idler e qualidade/estado do filamento.
> - Filamento de qualidade duvidosa (diâmetro irregular, umidade, composição) produz
>   sintomas idênticos a defeito mecânico ou de calibração. **Teste sempre com um
>   filamento de referência** antes de concluir que a máquina está errada.
> - A calibração da `rotation_distance` só é confiável com **idler regulado +
>   filamento bom**. Antes disso, cada medição dava um valor diferente (6.35, 4.97,
>   6.86...) porque o que se media era a patinagem, não o motor.
> - Os paliativos do diagnóstico (Velvet a 230 °C, MVS 14, rd alterada) foram
>   resposta ao sintoma, não à causa. **Reavalie/reverta se ainda estiverem ativos.**
>
> O restante do guia (hipóteses A–E, testes escalonados) fica como **registro
> histórico** e metodologia para casos futuros. Itens de "causa provável" refletem o
> raciocínio **antes** da resolução, não o desfecho.

## Resumo rápido: sub-extrusão na DXC2, por onde começar

1. **Filamento de referência**: PLA premium, seco, diâmetro consistente.
2. **Tensão do idler**: regular (ver abaixo). Mínima que elimina a patinagem.
3. **Limpar** os dentes da engrenagem (pó de filamento) e verificar rotação livre.
4. **Reapertar** os parafusos da montagem da engrenagem (afrouxam com ciclo térmico).
5. Só então: `rotation_distance` a 1 mm/s → teste escalonado → PA → flow ratio.

## Sintomas que levaram ao diagnóstico

Teste de Flow Rate do Creality Print (`0.20mm Standard PLA`, Voolt3D Velvet):

- Top layers com **gaps** entre as linhas.
- **Pinholes** espalhados, piores nas placas de flow mais negativo.
- A placa **+10%** foi a menos ruim → assinatura de **falta de material**.
- **Pó de filamento** dentro da extrusora, com as cores das últimas peças (PLA
  raspado, não metal) → patinagem real.
- Déficit **crescente com a velocidade**: para 100 mm pedidos, entraram 90 / 70 / 20 mm
  a 1 / 6 / 16,8 mm/s.

> Todas as placas do flow test saíram ruins: os "−20 / −9" eram só os *menos piores*
> de um lote sub-extrudado. O teste varria na direção errada.

## Ordem de calibração (de baixo pra cima)

1. Eliminar causas básicas: **filamento + tensão do idler**.
2. `rotation_distance` medida **a 1 mm/s** (nominal DXC2: **6.86**).
3. Teste escalonado (1 / 6 / 16,8 mm/s) com consumo de 99–101 mm nos três regimes.
4. `pressure_advance`.
5. Flow ratio no slicer (faixa saudável **0.95–1.05**).
6. Gravar flow ratio por material nos perfis de filamento.

Corrigir no flow ratio antes de isolar a causa física **mascara** o problema: você
grava um flow inflado em cada filamento e, no dia que a máquina for corrigida, todos
ficam errados juntos.

---

## Etapa 0 — Base: filamento e tensão do idler

### Filamento

- Use um **filamento premium de verdade** como referência (diâmetro 1,75 ± 0,02 mm,
  boa composição). Filamento barato/irregular gera sub-extrusão, pó e variação de
  medida que parece defeito de máquina.
- **Seco**: PLA ~45–50 °C por 6–8 h se houver dúvida.
- Se o problema some com outro filamento, a causa era o material. Teste isto
  **antes** de desmontar qualquer coisa.

### Tensão do idler

- Mecanismo: parafuso/botão comprime uma mola que empurra o idler contra o
  filamento. Apertar = mais mordida.
- Ajuste **1/4 de volta por vez**, retestando com o teste escalonado.
- Alvo: **mínima tensão que elimina a patinagem**. Demais achata o filamento e
  também piora; de menos, patina e gera pó.
- Neste setup a tensão mal regulada, somada ao filamento ruim, foi a causa real.
- Sinais de tensão errada: pó de filamento, déficit que cresce com a velocidade,
  estalos/pulos da engrenagem sob carga.

### Engrenagem de tração (hipótese E, descartada como causa final, mas verifique)

Problema documentado da DXC2: engrenagem arrastando no frame
([Phaetus Issue #3](https://github.com/Phaetus/DXC-2-Extruder/issues/3)).

- Sem filamento, gire a engrenagem à mão: deve ser **lisa** em toda a volta.
- Ponto de arrasto → solução do fabricante: afrouxar os 2 parafusos, girar até
  ficar suave, reapertar.
- Parafusos da montagem afrouxam com o ciclo térmico (~1/4 de volta): reaperte.
- **Limpe os dentes** (escova rígida/palito/ar comprimido). O pó entope os vãos e
  realimenta a patinagem.

---

## Etapa 1 — Calibrar `rotation_distance` (método do filamento marcado)

Método oficial do Klipper: <https://www.klipper3d.org/Rotation_Distance.html>.
Só faça com **idler regulado e filamento bom** (Etapa 0).

1. Hotend a **220 °C** (PLA), filamento carregado e fluindo (purge curto antes).
2. Escolha um **ponto de referência fixo** na entrada da extrusora. Marque o
   filamento a **120 mm** dele, medindo com paquímetro/régua rígida.
3. No console (Fluidd/Mainsail):

   ```gcode
   M83          ; extrusão relativa
   G1 E100 F60  ; 100 mm a 1 mm/s (lento = não derrapa)
   ```

   Espere (~100 s).
4. Meça a distância restante entre a marca e o ponto de referência (ideal: 20 mm).
5. Calcule:

   ```
   mm_reais = 120 − distância_restante
   nova_rd  = rotation_distance_atual × (mm_reais / 100)
   ```

   - `mm_reais` < 100 → entrou menos (a rd desce).
   - `mm_reais` > 100 → entrou mais (a rd sobe).
   - `mm_reais` entre 99 e 101 → mantenha o valor atual.
6. Edite `[extruder]` em `DXC2/printer.cfg`:

   ```ini
   [extruder]
   rotation_distance: <nova_rd>
   ```
7. `FIRMWARE_RESTART`.
8. **Repita o processo várias vezes** (passos 2–7) até `mm_reais` ficar entre
   **99 e 101 mm** de forma repetível. Neste setup foram várias iterações até
   convergir em **6.86**.

> **Regra do Klipper:** `rotation_distance` **menor** = **mais** material por rotação.

### Cuidados

- **Sempre a 1 mm/s (`F60`)**. Velocidade alta carrega o conjunto e embute o
  déficit na medição (foi assim que o 4.97, baixo demais, acabou gravado).
- Não calcule `nova_rd` com patinagem ativa (pó, idler frouxo, filamento ruim): você
  calibra o defeito, não o motor.
- PLA seco; úmido ou flexível (TPU) derrapa e falseia a medida.
- Se o bloco declarar `gear_ratio`, não mexa; calibre só a `rotation_distance`.
- Variação de ±5–10 mm na medição manual é normal; faça a média de 2–3 medidas.

### Histórico da `rotation_distance`

| Valor    | Origem                                          | Situação |
|----------|-------------------------------------------------|----------|
| 6.9      | `default/` (extruder stock K2)                  | referência stock |
| 6.35     | chute manual                                    | sem medir |
| 4.97     | medição a 16,8 mm/s com patinagem               | **errado** (contaminado por velocidade) |
| **6.86** | **valor do fabricante, confirmado por calibração repetida** | **FINAL** |

---

## Etapa 2 — Teste de velocidade escalonada (medidor de progresso)

Hotend a 220 °C, filamento carregado e seco. Meça o consumo real de 100 mm pedidos:

```gcode
M83
G1 E100 F60    ; 1 mm/s
G1 E100 F360   ; 6 mm/s
G1 E100 F1008  ; 16,8 mm/s
```

**Alvo:** os três regimes consumindo **99–101 mm**, sem gerar pó novo.

Resultado típico **antes** da correção (filamento ruim + idler mal regulado):

| Comando      | Velocidade | 220 °C     | 230 °C      |
|--------------|------------|------------|-------------|
| `E100 F60`   | 1 mm/s     | 90 (−10%)  | 110 (+10%)* |
| `E100 F360`  | 6 mm/s     | 70 (−30%)  | 70 (−30%)   |
| `E100 F1008` | 16,8 mm/s  | 20 (−80%)  | 20 (−80%)   |

\* Dentro da margem de medição manual e possivelmente com oozing a 230 °C.

Leitura: temperatura não alterou o déficit sob carga (descarta contrapressão como
causa principal). Consumo que cai com a velocidade = patinagem sob carga.

Se, depois de **filamento premium + idler regulado + engrenagem limpa e livre**,
ainda sobrar déficit, investigue o motor closed-loop (`klippy.log` durante o extrude
a 16,8 mm/s: track error, over-current, stall). O `motor_control.cfg` não tem bloco
`e_*`; **não edite PIDs/limites às cegas** (risco de superaquecer o motor/driver).
Peça o perfil do motor da DXC2 ao fabricante.

### Paliativos (só enquanto a causa não é fechada)

- **MVS**: `20 mm³/s` é agressivo para o bico stock 0.4 mm; faixa realista de PLA
  é 10–15 mm³/s. MVS é responsabilidade do **filamento**, não do processo.
- **Temperatura**: 230 °C não ajudou e pode amolecer o filamento e facilitar o
  descascamento. Voltar a ~220 °C.

Com a causa real resolvida, reavalie MVS e temperatura do perfil com o filamento
premium; os paliativos podem não ser mais necessários.

---

## Etapa 3 — Recalibrar `pressure_advance`

O PA atual (`0.038`) foi herdado do extruder stock. PA não afeta volume, só
cantos, seams e bleeding. Recalibre **depois** da `rotation_distance`.

### Opção A — Torre de PA do Creality Print / Orca

Calibration → Pressure Advance. Escolha a altura com cantos mais limpos.

### Opção B — `TUNING_TOWER` do Klipper

```gcode
SET_PRESSURE_ADVANCE ADVANCE=0
TUNING_TOWER COMMAND=SET_PRESSURE_ADVANCE PARAMETER=ADVANCE START=0 FACTOR=.005
```

```
PA = START + (FACTOR × altura_Z_boa)
```

Grave em `[extruder] → pressure_advance`, `FIRMWARE_RESTART`.
Faixa esperada para direct drive: **0.02–0.06**.

---

## Etapa 4 — Teste de Flow Rate no Creality Print

Só depois das etapas anteriores. Com a causa física resolvida, o flow ratio cai na
faixa saudável (**0.95–1.05**). Correção grande (>10%) no lado positivo = o déficit
persiste; volte à Etapa 0.

O teste é **multiplicativo em dois passos** sobre o flow do perfil:

```
flow_final = flow_de_partida × (1 + pass1/100) × (1 + pass2/100)
```

1. Zere o flow de partida (`flow_ratio = 1.0`) no perfil de filamento.
2. Filamento de referência + perfil `0.20mm Standard PLA`.
3. Pass1 (passos grandes): escolha a placa com topo mais fechado e uniforme.
4. Pass2 (passos finos): refine.
5. `flow_final = 1.0 × (1 + pass1/100) × (1 + pass2/100)`.

Avaliação das placas:

- **Sub**: gaps, pinholes, paredes translúcidas → mais flow (lado +).
- **Super**: topo inchado/rugoso, blobbing → menos flow (lado −).
- **Certo**: topo liso e fechado, dimensão no alvo.

Flow ratio é específico de bico+extrusora+material: rode ao menos uma vez por tipo
(PLA, PETG). Variação de marca dentro do tipo costuma ficar em ±2%.

## Etapa 5 — Gravar o flow ratio nos perfis

Grave `flow_ratio` e `max_volumetric_speed` calibrados. **Não** grave flows que
compensem erro físico (mecânico, vazão, temperatura): se a máquina for corrigida
depois, viram erro de extrusão. O perfil guarda o ajuste fino **do material**, não o
conserto da máquina.

---

## Checklist

- [x] Filamento premium de referência em uso.
- [x] Tensão do idler regulada.
- [x] `rotation_distance` calibrada a 1 mm/s em várias iterações → **6.86**.
- [x] `DXC2/printer.cfg` com `rotation_distance: 6.86` no `[extruder]` (o 6.35 anterior foi substituído).
- [ ] Teste escalonado (F60/F360/F1008): consumo 99–101 mm nos três regimes, sem pó novo.
- [ ] Reavaliar paliativos (Velvet 230 → 220 °C; MVS 14) com o filamento premium.
- [ ] `pressure_advance` recalibrado para a DXC2 (0.02–0.06).
- [ ] Flow Rate do CP com perfil zerado em 1.0; flow final 0.95–1.05.
- [ ] `flow_ratio`/MVS gravados nos perfis de filamento.

## Referências

- Klipper — Rotation distance: <https://www.klipper3d.org/Rotation_Distance.html>
- Klipper — Pressure advance: <https://www.klipper3d.org/Pressure_Advance.html>
- Phaetus DXC-2 Issue #3: <https://github.com/Phaetus/DXC-2-Extruder/issues/3>
- The Incompetent Craftsman, "Creality K2 Plus Calibrating E Steps - Rotation
  Distance": <https://www.youtube.com/watch?v=HjecuraOoqI> (método usado na
  calibração deste setup).

*Conteúdo das referências parafraseado para conformidade de licenciamento.*
