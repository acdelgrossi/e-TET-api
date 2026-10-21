# Escala de Risco Familiar de Coelho-Savassi

Este documento registra a versão da escala adotada pelo e-TET, a regra de
pontuação aplicada e o histórico de correção do cálculo.

## Versão adotada

O e-TET segue a **versão sistematizada** da Escala de Risco Familiar de
Coelho-Savassi, conforme o **Quadro 2** do projeto de pesquisa e o artigo
original dos autores.

Referência: SAVASSI, L. C. M.; LAGE, J. L.; COELHO, F. L. G. Sistematização de
um instrumento de estratificação de risco familiar: Escala de risco familiar de
Coelho-Savassi. *Journal of Management & Primary Health Care*, v. 3, n. 2,
p. 179-185, 2012.

Não há adaptação local dos pesos. Qualquer divergência entre o código e a tabela
abaixo deve ser tratada como defeito.

## Pontuação das sentinelas

| Sentinela | Pontos |
|---|---|
| Pessoa acamada | 3 |
| Deficiência física | 3 |
| Deficiência mental | 3 |
| Baixas condições de saneamento | 3 |
| Desnutrição grave | 3 |
| Drogadição | 2 |
| Desemprego | 2 |
| Analfabetismo | 1 |
| Criança com menos de 6 meses | 1 |
| Pessoa com mais de 70 anos | 1 |
| Hipertensão arterial sistêmica | 1 |
| Diabetes mellitus | 1 |
| Relação morador/cômodo > 1 | 3 |
| Relação morador/cômodo = 1 | 2 |
| Relação morador/cômodo < 1 | 0 |

As sentinelas de caráter individual são somadas por pessoa: se duas pessoas da
mesma família são acamadas, a família recebe 6 pontos por essa sentinela.

A relação morador/cômodo é o número de moradores não arquivados da família
dividido pelo número de cômodos do domicílio.

## Classificação

| Escore | Classificação |
|---|---|
| 0 a 4 | Risco Baixo (R0) |
| 5 e 6 | Risco Menor (R1) |
| 7 e 8 | Risco Médio (R2) |
| 9 ou mais | Risco Máximo (R3) |

Os textos gravados no banco são exatamente `Risco Baixo`, `Risco Menor`,
`Risco Médio` e `Risco Máximo`, conforme o enum `FamilyRisk`. Nenhum outro valor
deve ser gravado na coluna `classificacao_risco`.

## Onde o cálculo vive

A mesma fórmula existe em dois lugares, e os dois precisam ser alterados juntos:

- `e-TET-api`: `src/feats/families/services/risk-calculator.service.ts`,
  método `calculateScoreAndClass`. Os testes ficam no `.spec.ts` ao lado.
- `e-ACS-front`: `src/stores/familyStore.js`, dentro de `recordRisk`. É a cópia
  usada para exibição imediata quando o aplicativo está sem conexão.

Alterar apenas um dos dois faz o agente ver um escore na rua e outro depois de
sincronizar.

## Trilha de auditoria

**21/10/2026 — correção dos pesos.** Até esta data o cálculo divergia do
Quadro 2 em cinco pontos: analfabetismo, criança menor de 6 meses e pessoa com
mais de 70 anos pontuavam 2 em vez de 1, e a relação morador/cômodo pontuava
1 e 2 em vez de 2 e 3.

Com os pesos corrigidos, o exemplo publicado pelos autores (dois acamados, um
deles com 75 anos e hipertenso, o outro com deficiência física, ambos
analfabetos) passa a resultar em 13 pontos, como na referência. Antes resultava
em 16.

As famílias já cadastradas foram recalculadas a partir das sentinelas guardadas
na tabela `family_risks`, usando o registro mais recente de cada família. Das
oito famílias com estratificação registrada, cinco mantiveram o escore, três
tiveram o escore ajustado e uma mudou de classificação (de Risco Médio para
Risco Máximo).
