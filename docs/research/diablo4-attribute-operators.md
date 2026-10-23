# Investigação: atributos internos do Diablo IV

Status: investigação inicial; hipóteses ainda não validadas.

## Fonte
- https://github.com/DiabloTools/d4data
- `attributes.json`: objeto indexado por nome, 1.393 entradas observadas.
- `attributeList.json`: lista de 1.394 entradas observadas.
- Os dados são extraídos do jogo; versão, licença e cobertura precisam ser verificados antes de qualquer importação.

## Estrutura observada
Campos: `eAttrib`, `name`, `defaultValue`, `eParameterType`, `eAttributeOperator`, `attributeDataType`, `formula`, `syncFlags`, entre outros.

### Operadores — distribuição observada em attributeList.json
| Código | Quantidade |
| --- | ---: |
| 0 | 527 |
| 1 | 487 |
| 2 | 86 |
| 3 | 169 |
| 4 | 2 |
| 5 | 2 |
| 6 | 49 |
| 7 | 66 |
| 8 | 1 |
| 9 | 2 |
| 10 | 2 |
| 11 | 1 |

**Não há mapeamento confirmado** entre esses códigos e operações como soma/multiplicação. O campo pode representar política de agregação ou outro comportamento interno; é hipótese, não conclusão.

## Evidências de fórmulas
`attributeList.json` contém 71 registros com `formula` não nula na versão consultada.

Exemplo literal (`Strength_Total`):

```text
(Strength     + Strength_Core     + Plus_All_Stats + Core_Stats_Bonus_Strength)     * (1.0 + (Strength_Percent_Bonus     + All_Stats_Percent_Bonus))
```

Exemplos de identificadores: `Strength` (4), `Intelligence` (5), `Willpower` (6), `Dexterity` (7), `Vulnerable` (735), `Vulnerable_Health_Damage_Bonus` (736), `Multiplicative_Vulnerable_Health_Damage_Bonus` (737).

## Perguntas em aberto
1. Qual a definição original de `eAttributeOperator`? Procurar enumeração ou implementação do agregador.
2. Por que a lista tem uma entrada a mais que o objeto indexado?
3. Como mapear atributos internos aos afixos visíveis em equipamentos?
4. Onde ficam intervalos, restrições por slot/classe e localização dos nomes?
5. Qual versão do jogo os arquivos representam e quais condições de reutilização se aplicam?

## Próximos passos
- Comparar entradas por `eAttrib` para explicar a diferença entre arquivos.
- Investigar scripts, definições e histórico de extração.
- Registrar evidências, hipóteses e confiança separadamente.
- Não integrar dados ao backend nem alterar a aplicação nesta fase.

## Descoberta adicional: diferença de um registro (confirmada)
A lista possui dois registros com o mesmo nome `Multiplicative_Damage_Bonus_Percent_To_Weakened`:
- `eAttrib=742`, `eParameterType=-1`, `eAttributeOperator=7`;
- `eAttrib=743`, `eParameterType=5`, `eAttributeOperator=7`.

O objeto `attributes.json` é indexado por nome, portanto não pode representar ambas as entradas como chaves distintas. Isso **explica a diferença de contagem** (1.394 contra 1.393) e revela risco de perda de informação ao usar o objeto indexado por nome como fonte canônica. Preferir a lista e preservar `eAttrib` como identificador, sujeito a validação de unicidade.

## Pistas sobre operadores (hipóteses, não enumeração validada)
- Código 6 aparece em `Resource_Cost_Reduction_Percent` e `Power_Cooldown_Reduction_Percent`.
- Código 7 aparece em `Multiplicative_Damage_Bonus_Percent_To_Weakened` e `Debug_Damage_Percent_Multiply`.
- Código 0 aparece em `Strength_Total` com fórmula explícita de soma e multiplicação, o que demonstra que **o código não é simplesmente o operador matemático escrito na fórmula**.

Esses agrupamentos sugerem semântica de agregação, mas não permitem afirmar qual é a operação de cada código sem a definição original ou testes adicionais.

## Proveniência e licenciamento (verificação adicional)
- `buildVersion.txt` no repositório consultado contém **`3.2.3.73883`**. Registrar essa string como versão declarada pelo dataset, **sem presumir equivalência com a versão comercial/temporada atual**.
- O repositório possui um arquivo `LICENSE` com licença **MIT**, copyright 2023 blizzhackers.
- O próprio `readme.md` ressalva que os **assets diretamente extraídos do jogo continuam sendo propriedade da Blizzard**. Portanto, a licença MIT do código do repositório não é autorização irrestrita para redistribuir assets do jogo.
- O README descreve `DT_SNO` e `DT_SNONAME` como referências a arquivos; `DT_GBID` como referência a elemento específico de um array em arquivo de game balance.

Fontes: [LICENSE](https://github.com/DiabloTools/d4data/blob/master/LICENSE), [README](https://github.com/DiabloTools/d4data/blob/master/readme.md), [buildVersion.txt](https://github.com/DiabloTools/d4data/blob/master/buildVersion.txt).

## Cruzamento entre DiabloTools e WiseOwl (23/10/2026)

**Resultado: não é seguro fazer join por ID entre versões sem uma camada de reconciliação.**

O WiseOwl documenta exemplos para build `3.1.1.72836` (temporada 14), enquanto o `buildVersion.txt` do DiabloTools consultado declara `3.2.3.73883`. Comparações pontuais com o `attributeList.json`:

| ID | WiseOwl (nome apresentado) | DiabloTools (nome interno) | Situação |
| --- | --- | --- | --- |
| 9 | Strength | Strength_Core | Semântica relacionada, não idêntica |
| 133 | Maximum Life | Flat_Hitpoints_Max_Bonus | Possível relação; requer confirmação |
| 142 | Maximum Life | Hitpoints_Max_Percent_Bonus | Divergência de granularidade |
| 275 | Critical Strike Chance | Crit_Percent_Bonus | Compatível conceitualmente, não comprovado como equivalência |
| 481 | Armor (pré-S14, conforme WiseOwl) | Core_Stat_Armor_Bonus | Divergência explícita |
| 482 | Armor (S14) | Armor_Percent | Divergência de descrição/granularidade |
| 953 | Damage to Elites | Damage_Percent_Bonus_Vs_Elites | Compatível conceitualmente, não comprovado como equivalência |

O WiseOwl documenta ainda dois espaços de identificadores: IDs positivos para `eAttribute` do motor e IDs com bit alto ligado para `DataAttributes` (tabela SNO 1907204). Não usar valor absoluto de IDs negativos. A identidade de um efeito de afixo inclui `(AttributeId, ParamPlus12)`; o parâmetro pode representar elemento ou tag de habilidade, e `0xFFFFFFFF` é sentinela de ausência. Para magnitudes, o WiseOwl distingue `FormulaGbid` (tabela `AttributeFormulas`, SNO 201912), `InlineFormula` e `StaticValues`. Portanto, **identidade do atributo e cálculo do valor são problemas diferentes**.

**Limite da evidência:** estes são exemplos publicados na documentação e registros do dataset; ainda não cruzamos um mesmo registro bruto de `AffixDefinition` nas duas fontes, nem validamos equivalência entre versões.

Fontes: [WiseOwl AttributeNames](https://github.com/WiseOwlSoftware/WiseOwl.Casc/blob/main/docs/api/WiseOwl.Casc.Diablo4/WiseOwl.Casc.Diablo4/AttributeNames.md), [WiseOwl AffixEffect](https://github.com/WiseOwlSoftware/WiseOwl.Casc/blob/main/docs/api/WiseOwl.Casc.Diablo4/WiseOwl.Casc.Diablo4/AffixEffect.md), [DiabloTools attributeList](https://github.com/DiabloTools/d4data/blob/master/attributeList.json).

### Próxima fronteira
- Obter amostras brutas versionadas de afixos e atributos da **mesma build**.
- Validar `AttributeId`, `ParamPlus12` e nomes localizados em conjunto.
- Investigar os dois namespaces e mudanças de ordinal entre builds.
- Só depois vincular `FormulaGbid` / `InlineFormula` e estudar os operadores.

Contagens do dataset revalidadas nesta rodada: 1.394 entradas na lista, 1.393 chaves no objeto, 71 fórmulas não nulas e distribuição dos 12 operadores idêntica à tabela acima.

## Parecer de encerramento da pesquisa exploratória (23/10/2026)

**Decisão:** encerrar o levantamento exploratório de fontes e riscos de modelagem, não a validação completa do pipeline de afixos. Esta PR é documental.

### Achados
- Preferir attributeList.json: o objeto por nome perde a distinção entre os IDs 742 e 743.
- Preservar build, proveniência, namespace e parâmetro na identidade dos efeitos.
- WiseOwl documenta AttributeId, ParamPlus12, FormulaGbid, InlineFormula e StaticValues, mas isso não comprova equivalência de IDs entre builds.
- Os 12 códigos eAttributeOperator estão catalogados; sua semântica matemática segue desconhecida.

### Validações pendentes (não executadas nesta PR)
1. Congelar uma build e registrar checksums de amostras brutas.
2. Testar um afixo comum: atributo, parâmetro, fórmula e intervalo.
3. Testar um afixo com múltiplos modificadores, preservando todos os efeitos.
4. Testar um afixo com InlineFormula ou StaticValues e sentinelas ausentes.
5. Comparar nomes localizados e registrar divergências.

### Condição de aprovação
Aprovar esta PR significa aceitar documentação de pesquisa exploratória com limites explícitos. **Não significa aprovar normalizador, resolvedor de fórmulas, equivalência entre versões ou motor de dano.** Nenhum teste automatizado foi executado nesta PR. A implementação e a validação prática exigem trabalho separado.
