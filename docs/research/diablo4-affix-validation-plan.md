# Validação de afixos — fase 2

Status: planejamento de testes, sem validação empírica concluída.

## Objetivo
Validar a cadeia AffixDefinition / AffixEffect → identidade de atributo → origem da magnitude usando amostras reais da **mesma build**.

## Contrato de identidade
- Registrar source, build, arquivo e checksum de cada amostra.
- Preservar namespace, AttributeId e ParamPlus12 (não usar abs() para IDs negativos).
- Preservar todos os modificadores de um afixo, incluindo repetições de nome.
- Tratar 0xFFFFFFFF como sentinela somente onde o esquema do campo documentar essa convenção.
- Distinguir FormulaGbid, InlineFormula e StaticValues sem presumir precedência não verificada.

## Matriz de testes
| Caso | Entrada necessária | Asserção | Status |
| --- | --- | --- | --- |
| Afixo comum (chance crítica) | registro bruto e tabela de atributos da mesma build | resolve ID, parâmetro e fórmula, preservando provenance | pendente |
| Múltiplos modificadores | registro bruto com pelo menos dois efeitos | não descarta nem mescla efeitos | pendente |
| Fórmula inline/estática | registro bruto com campo correspondente | classifica corretamente a fonte de magnitude e sentinelas | pendente |
| Duplicidade de nome | entradas eAttrib 742 e 743 | preserva ambos os registros | documentado, teste pendente |
| Cross-build | duas builds identificadas | rejeita join por ID sem reconciliação | pendente |

## Regras de aceite
1. Fixar build e checksum das amostras, com fonte e licença documentadas.
2. Usar dados brutos e resultados esperados explícitos; não usar exemplos inventados como prova.
3. Executar testes automatizados e registrar comandos, saída e eventuais falhas.
4. Separar evidência observada de hipótese e não declarar o motor de dano validado.
5. Submeter a PR para revisão sem merge automático.

## Referências
- https://github.com/DiabloTools/d4data
- https://github.com/WiseOwlSoftware/WiseOwl.Casc
- docs/research/diablo4-attribute-operators.md
