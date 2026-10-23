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
