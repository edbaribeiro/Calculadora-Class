# Calculadora de Jornada - Calendário

Ferramenta HTML de arquivo único para calcular carga horária de aulas/turnos
a partir de um calendário interativo. Não precisa de instalação, servidor ou
build: basta abrir o arquivo `calculadora-jornada-calendario.html` em
qualquer navegador.

## Como usar

1. **Navegue até o mês/ano** desejado com as setas ou os seletores no topo.
2. **Selecione os dias**:
   - Clique em um dia do calendário para marcá-lo/desmarcá-lo individualmente.
   - Ou clique em um botão de dia da semana (Dom, Seg, Ter...) para marcar
     automaticamente todas as ocorrências daquele dia no mês exibido. Clicar
     de novo desmarca todas.
   - "Limpar mês" desmarca todos os dias do mês exibido.
   - As seleções são mantidas ao trocar de mês/ano, permitindo montar um
     período que atravessa vários meses.
3. **Preencha os campos**:
   - **Início / Término**: horário da aula/turno, aplicado a todos os dias
     marcados.
   - **Minutos adicionais / hora**: minutos extras somados por hora cheia do
     intervalo (ver regra abaixo).
   - **Aulas por semana**: quantidade de aulas semanais, usada no cálculo da
     carga horária semanal.
   - **Minutos / hora cheia**: minutos extras de uma segunda regra
     independente, também por hora cheia (ver abaixo).
4. Os resultados são recalculados automaticamente e exibidos em dois
   formatos: **relógio** (`Xh YYm`) e **decimal** (`X,YY`).

## Cartões de resultado

| Cartão | O que mostra |
|---|---|
| Por dia (bruto / c/ adicional) | Duração de uma aula: intervalo puro → intervalo + minutos adicionais |
| Total do período (c/ adicional) | Soma de "por dia com adicional" × dias selecionados no calendário |
| Carga horária semanal × 4,5 | (duração por dia c/ adicional) × aulas por semana, e esse valor × 4,5 (multiplicador fixo, projeção mensal aproximada) |
| Adicional por hora cheia | Minutos extras da segunda regra (campo "Minutos / hora cheia"), por dia e somados no total do período |

## Regras de cálculo

**Horas cheias**: para os dois campos de minutos adicionais, uma "hora
cheia" é `Math.floor(intervalo_em_minutos / 60)`.

- **Minutos adicionais / hora** (afeta "Por dia", "Total do período" e
  "Carga horária semanal"): soma-se o valor do campo uma vez para cada hora
  cheia do intervalo bruto, com **mínimo de 1 vez** mesmo se a aula durar
  menos de 60 minutos.
  Exemplo: intervalo de 50 min + 10 min adicionais → 1 hora cheia (mínimo) ×
  10 = 60 min.
  Exemplo: intervalo de 3h00 (180 min) + 10 min adicionais → 3 horas cheias ×
  10 = 30 min → total 3h30.
- **Minutos / hora cheia** (cartão "Adicional por hora cheia", independente
  do campo acima): soma-se o valor do campo uma vez para cada hora cheia do
  intervalo **bruto, sem o adicional já aplicado**, **sem mínimo** — um
  intervalo com menos de 60 min gera 0 horas cheias e portanto 0 adicional.
  Exemplo: intervalo de 3h00 (180 min), 30 min/hora cheia, 8 dias
  selecionados → 3 horas × 30 min = 1h30 por dia × 8 dias = 12h.
- **Carga horária semanal × 4,5**: `(duração por dia com adicional) × aulas
  por semana`, e esse resultado multiplicado por **4,5** (fixo, não
  editável) — usado como projeção de carga mensal a partir da carga semanal.
- **Decimal**: sempre com vírgula (padrão brasileiro), sem sufixo "h" —
  ex.: `9,00`, `40,50`.

## Tecnologia

Um único arquivo `.html`, sem etapa de build. Usa React 18 + Babel Standalone
(compilação JSX no navegador) e Tailwind CSS, todos carregados via CDN:

- `cdn.tailwindcss.com`
- `unpkg.com/react@18` e `react-dom@18`
- `unpkg.com/@babel/standalone`
- `unpkg.com/lucide` (ícones)

Por depender de CDNs, é necessário estar **conectado à internet** para abrir
o arquivo corretamente.

## Limitações conhecidas

- Não trata turnos que cruzam a meia-noite (ex.: 22:00–06:00); se
  Término < Início, o intervalo bruto do dia é considerado 0.
- Todos os dias selecionados usam o mesmo horário de Início/Término — não é
  possível definir horários diferentes por dia individualmente.
