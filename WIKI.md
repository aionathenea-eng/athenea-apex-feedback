# Athenea Apex - Guia Educacional Completo

> Este ficheiro serve como Wiki de referencia para todos os conceitos matematicos e metodologicos por detras do Athenea Apex.

---

## Indice

1. O que e Value Betting?
2. Metodo Shin - Devigging Explicado
3. Kelly Criterion - Dimensionamento Optimo de Apostas
4. CLV - Closing Line Value
5. Como o Athenea Apex Funciona
6. Links e Recursos

---

## 1. O que e Value Betting?

**Value betting** e a pratica de apostar em resultados cujas probabilidades reais sao superiores as probabilidades implicitas nas odds da casa de apostas.

### A Formula do Expected Value (EV)

```
EV = (probabilidade_real x lucro_potencial) - (probabilidade_falha x stake)
```

**Exemplo pratico:**
- Casa oferece odds de **2.20** numa equipa (implica 45.5% de probabilidade)
- O nosso modelo estima **52%** de probabilidade real
- EV = (0.52 x 1.20) - (0.48 x 1) = **+0.144** por unidade apostada

Um EV positivo significa que, repetindo apostas similares centenas de vezes, o lucro esperado e positivo - independentemente do resultado individual.

### Por que funciona?

As casas de apostas definem odds com base em:
1. Probabilidades de mercado (fluxo de apostas)
2. Margem embutida (*vig*)
3. Limitacoes de modelo para mercados menores

O Athenea Apex explora ineficiencias sistemicas, especialmente em mercados secundarios onde os modelos das casas sao mais fracos.

---

## 2. Metodo Shin - Devigging Explicado

### O Problema da Vig

Quando ves odds de 1.90/1.90 num mercado de dois resultados, a soma das probabilidades implicitas e **105.3%** - 5.3% a mais que 100%. Essa diferenca e a margem da casa (*overround* ou *vig*).

Para encontrar probabilidades reais, precisamos de **remover a vig** - processo chamado *devigging*.

### Por que Shin e nao outros metodos?

Existem varios metodos (proporcional, aditivo, power), mas o **Metodo Shin** e o mais sofisticado porque modela explicitamente a existencia de **apostadores informados** (insiders) no mercado.

O modelo assume que uma fraccao *z* das apostas vem de apostadores com informacao privilegiada, e resolve iterativamente para encontrar as probabilidades que maximizam consistencia.

### Como funciona

1. Parte das odds brutas de cada resultado
2. Estima a proporcao de apostadores informados no mercado (z)
3. Resolve um sistema de equacoes iterativo
4. Devolve probabilidades normalizadas a 100%

```python
# Pseudocodigo simplificado
def shin_devig(odds_list):
    implied = [1/o for o in odds_list]
    vig = sum(implied) - 1
    z = solve_shin_z(implied, vig)
    true_probs = [(p - z*p) / (1 - z) for p in implied]
    return normalize(true_probs)
```

---

## 3. Kelly Criterion - Dimensionamento Optimo de Apostas

### A Formula

Desenvolvido por John Kelly Jr. nos Bell Labs em 1956, o Kelly Criterion determina a fraccao ideal do bankroll a apostar para maximizar o crescimento a longo prazo:

```
f* = (b x p - q) / b

Onde:
  f* = fraccao do bankroll a apostar
  b  = odds decimais - 1 (lucro por unidade)
  p  = probabilidade estimada de ganhar
  q  = 1 - p (probabilidade de perder)
```

### Exemplo Pratico

- Odds: **2.30** => b = 1.30
- Probabilidade real estimada: **50%** => p = 0.50, q = 0.50
- Kelly = (1.30 x 0.50 - 0.50) / 1.30 = **0.115** = 11.5% do bankroll

### Kelly Fracionado

O Kelly puro pode ser demasiado agressivo. Usamos **1/4 Kelly** (2.875% neste exemplo) para:
- Reduzir variancia sem sacrificar muito crescimento
- Proteger contra erros de estimativa de probabilidade
- Manter drawdowns psicologicamente toleraveis

### Por que nao apostar valor fixo?

Apostar sempre o mesmo valor ignora:
- A magnitude do edge (um EV de 15% merece mais que um de 1%)
- O tamanho actual do bankroll
- A incerteza na estimativa de probabilidades

Kelly resolve tudo isto de forma matematicamente optima.

---

## 4. CLV - Closing Line Value

### O que e?

**CLV (Closing Line Value)** mede se as odds nas quais apostaste eram melhores que as odds de fecho do mercado antes do evento comecar.

```
CLV% = (odds_entrada / odds_fecho - 1) x 100
```

### Por que e a metrica mais importante?

As odds de fecho representam o **consenso mais eficiente do mercado** - incorporam toda a informacao disponivel antes do evento. Bater consistentemente as odds de fecho e prova matematica de edge real, independentemente dos resultados de curto prazo.

**Exemplo:**
- Apostaste a **2.40** num resultado
- Odds de fecho: **2.10**
- CLV = (2.40/2.10 - 1) x 100 = **+14.3%**

Um CLV positivo consistente (mesmo que percas muitas apostas) demonstra que o teu processo de seleccao e superior ao mercado.

### CLV vs Resultados

| Metrica | O que mede | Fiavel a curto prazo? |
|---------|-----------|----------------------|
| P&L | Lucro/Perda real | Nao (alta variancia) |
| ROI | Retorno sobre investido | Nao (menos de 500 apostas) |
| **CLV** | **Qualidade do processo** | **Sim (desde 50 apostas)** |

---

## 5. Como o Athenea Apex Funciona

### Pipeline Completo

```
Odds de Mercado (200+ casas via API)
         |
   Shin Devigging (probabilidades reais)
         |
   Calculo de EV
         |
   Filtro de Edge (EV minimo configuravel)
         |
   Kelly Fracionado (dimensionamento da stake)
         |
   Pick publicado no Telegram
   (odds, stake, EV estimado, liga)
         |
   Tracking CLV pos-evento
   (validacao de edge continua)
```

### O que recebes em cada pick

- Liga e jogo
- Mercado especifico (1X2, Asian Handicap, Over/Under, etc.)
- Odds recomendadas e casa de apostas
- Stake em unidades Kelly (ex: 0.5u, 1u, 2u)
- EV estimado (ex: +8.3%)
- Probabilidade Shin calculada

### Transparencia Total

Todos os picks sao registados com timestamp antes do evento. O CLV e calculado automaticamente apos fecho. As metricas de edge sao publicas no canal gratuito.

---

## 6. Links e Recursos

| Recurso | Link |
|---------|------|
| Website Oficial | [atheneaapex.com](https://atheneaapex.com) |
| Canal Telegram Gratuito | [@atheneaapexfree](https://t.me/atheneaapexfree) |
| Bot de Picks | [@AtheneaApexBot](https://t.me/AtheneaApexBot) |
| Subscrever (Whop) | [whop.com/athenea-apex](https://whop.com/athenea-apex/) |
| Feedback e Roadmap | [GitHub Issues](https://github.com/aionathenea-eng/athenea-apex-feedback/issues) |

---

### Leitura Adicional

- [Kelly, J.L. (1956) - A New Interpretation of Information Rate](https://en.wikipedia.org/wiki/Kelly_criterion)
- [Shin, H.S. (1992) - Prices of State Contingent Claims with Insider Traders](https://www.jstor.org/stable/2234526)
- [Closing Line Value - The Professionals Holy Grail](https://www.pinnacle.com/en/betting-articles/Handicap-Betting/closing-line-value/G9BM9PRKHYKFPNT2)

---

*Athenea Apex (c) 2025 - Value Betting com Matematica de Elite*