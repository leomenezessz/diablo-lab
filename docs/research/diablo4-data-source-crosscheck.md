# Fontes independentes para validação dos atributos

Status: pistas externas, sem validação da semântica dos operadores.

## Continuidade do projeto
- Projeto original arquivado: https://github.com/blizzhackers/d4data (arquivado em julho de 2024).
- Continuação utilizada na investigação: https://github.com/DiabloTools/d4data.

## Implementações para comparação
- https://github.com/shardworks/d4-tools — descreve pipeline de importação de dados de DiabloTools/d4data e documentação de motor de dano. **Ainda não verificamos sua interpretação de `eAttributeOperator`.**
- https://github.com/Dakota628/d4parse — parser em Go de arquivos do Diablo IV, com gerador de structs baseado em `definitions.json`. Pode esclarecer a estrutura dos dados, mas não necessariamente a semântica matemática.
- https://github.com/ALCHElVlY/DiabloIVData — fork de d4data para comparar versões e definições.

## Método proposto
1. Buscar definição explícita do enum ou consumidor do campo `eAttributeOperator`.
2. Distinguir operador de agregação de expressões do campo `formula`.
3. Cruzar interpretações independentes e registrar evidência verificável por arquivo/linha/versão.
4. Só depois escrever testes de hipótese e mapear códigos.

**Não assumir** que um nome contendo `Multiplicative` prova a operação implementada. Nenhuma alteração funcional foi feita.
