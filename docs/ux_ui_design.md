# UX/UI Design Specification — Sistema de Análise Contábil

## Visão Geral
Este documento descreve a experiência do usuário e o design de interface para um sistema de análise de dados contábeis inspirado em plataformas como o Power BI e a Nucont. O produto transforma balancetes contábeis em dashboards interativos, permitindo que analistas financeiros, contadores e gestores acompanhem indicadores críticos mês a mês.

## Personas Primárias
1. **Analista Financeiro** — precisa monitorar indicadores financeiros e de performance em tempo real e gerar insights rápidos para a diretoria.
2. **Contador Consultor** — utiliza os dashboards para comparar períodos, validar lançamentos e preparar relatórios para clientes.
3. **Gestor Executivo** — acessa o painel para visualizar KPIs resumidos e tomar decisões estratégicas.

## Fluxo do Usuário
1. **Importação do balancete:** usuário seleciona arquivo Excel (.xlsx) do balancete contábil.
2. **Mapeamento automático de contas:** sistema sugere classificação das contas (ativo, passivo, receita, despesa) e permite ajustes.
3. **Tratamento dos dados:** interface exibe prévia dos lançamentos e destaca inconsistências (débitos ≠ créditos) antes de prosseguir.
4. **Geração do dashboard:** usuário confirma, o sistema calcula saldos mensais (débito − crédito) e renderiza gráficos.
5. **Exploração:** usuário filtra, compara períodos e exporta insights.

## Arquitetura de Informação
- **Barra lateral esquerda:** navegação entre páginas (Dashboard Geral, Indicadores Contábeis, Relatórios, Configurações) e status da última atualização.
- **Header fixo:** título do workspace, seletor de empresa, botão de upload rápido e filtros globais de período.
- **Área principal:** mosaico de cards e gráficos organizados em linhas responsivas.
- **Painel de filtros dinâmicos:** expande à direita com opções de centro de custo, conta contábil, projeto e tags customizadas.

## Onboarding e Feedback
- **Assistente de importação com 3 passos** (Upload → Validação → Resumo), com feedback visual (progress bar e badges de sucesso/erro).
- **Notificações contextuais** para avisar sobre ajustes necessários (ex.: "Conta 3.1.1 sem classificação. Clique para definir.").
- **Tooltips educativos** comparando valores com padrões do setor (benchmark Nucont) para acelerar compreensão.

## Layout do Dashboard Principal
1. **Visão Geral (linha 1)**
   - Card "Receita Líquida" com variação vs. mês anterior.
   - Card "Resultado Operacional".
   - Card "Margem Líquida" com indicador de tendência.
   - Card "Saldo de Caixa".

2. **Análise Temporal (linha 2)**
   - **Gráfico de linhas empilhadas:** evolução mensal dos saldos de classes contábeis (ativo, passivo, patrimônio, resultado). Cada série é calculada por `soma(débito − crédito)` para as contas da classe.
   - **Gráfico de colunas clusterizadas:** comparação mensal entre Receitas, Custos e Despesas Operacionais.

3. **Indicadores Contábeis (linha 3)**
   - Card interativo em formato de tabela com KPIs inspirados na Nucont.
   - Modo "semáforo" (verde/amarelo/vermelho) com thresholds configuráveis.

4. **Caixa e Liquidez (linha 4)**
   - **Gráfico de área** com saldo de caixa acumulado mês a mês.
   - **Gráfico de barras horizontais** para prazos médios (PMR, PMP, PME).

5. **Alertas e Recomendações (linha 5)**
   - Lista ordenada de insights gerados automaticamente (ex.: "Despesas administrativas cresceram 18% no último trimestre").

## Componentes-Chave
- **Upload Card:** arraste & solte ou botão "Selecionar arquivo", com checklist de requisitos (formato, colunas obrigatórias, total de débitos = total de créditos).
- **Tabela de pré-visualização:** permite agrupar, filtrar e editar classificações antes da geração do dashboard.
- **Toolbar de filtros globais:** seletor de período com opção "Comparar com mesmo período do ano anterior".
- **Modo de apresentação:** alterna layout para TVs/visores, ocultando controles e ampliando gráficos.

## Estratégia de Visualização de Dados
- Todos os gráficos exibem dados **mensalizados**. O sistema soma lançamentos por mês e calcula `saldo = total_débitos − total_créditos` para cada conta.
- Indicadores que exigem acumulado (ex.: Margem Líquida) usam a fórmula `saldo_receita - saldo_despesas` dividido pelo total de receitas do período.
- Gráficos suportam **comparação entre períodos** (Mês vs. Mês anterior, Ano vs. Ano anterior) via overlays ou séries secundárias tracejadas.

## Indicadores Contábeis (Baseline Nucont)
| Indicador | Fórmula (usando saldo = débitos − créditos) | Insight principal | Visual sugerido |
|-----------|----------------------------------------------|-------------------|------------------|
| Margem Líquida | `(Receita Operacional Líquida − Despesas Totais) / Receita Operacional Líquida` | Lucro em relação à receita | Card com trendline |
| EBITDA | `Resultado Operacional + Depreciação + Amortização` | Desempenho operacional | Card + mini sparkline |
| Ponto de Equilíbrio | `Custos Fixos / (1 − Margem de Contribuição)` | Volume mínimo para não ter prejuízo | Gauge horizontal |
| Margem de Contribuição | `(Receita − Custos Variáveis) / Receita` | Eficiência das vendas | Card com chips de produto |
| Giro de Caixa | `Recebimentos Médios / Pagamentos Médios` | Liquidez de curto prazo | Barra horizontal comparativa |
| Prazo Médio de Recebimento (PMR) | `Contas a Receber / Receita Média Diária` | Tempo para receber vendas | Barra horizontal |
| Prazo Médio de Pagamento (PMP) | `Contas a Pagar / Custo Médio Diário` | Tempo para pagar fornecedores | Barra horizontal |
| Prazo Médio de Estocagem (PME) | `Estoque / Custo de Mercadorias Vendidas Diário` | Eficiência do estoque | Barra horizontal |
| Ciclo Financeiro | `PMR + PME − PMP` | Necessidade de capital de giro | Gráfico de linhas com meta |
| Endividamento Geral | `Passivo Total / Ativo Total` | Estrutura de capital | Card com semáforo |
| Liquidez Corrente | `Ativo Circulante / Passivo Circulante` | Capacidade de honrar obrigações | Card com gauge |

## Interações e Microinterações
- **Hover em pontos do gráfico:** tooltip com valor do mês, variação percentual e comentário automático (ex.: "acima da média trimestral").
- **Clique duplo em série:** isola a série e aplica destaque visual.
- **Context menu** em cards para exportar dados (CSV, PDF) ou fixar card em dashboard personalizado.
- **Animação suave** ao atualizar filtros para reforçar sensação de continuidade.

## Acessibilidade
- Paleta com contraste mínimo 4.5:1 para texto principal e 3:1 para elementos de interface.
- Gráficos com padrões/linhas auxiliares para diferenciar séries além das cores.
- Suporte a navegação por teclado (foco visível, atalhos para trocar período, abrir filtros).
- Textos alternativos nos cards para leitores de tela com descrição dos insights.

## Responsividade
- **Desktop (≥1280px):** layout em 4 colunas com cards médios.
- **Tablet (768–1279px):** layout em 2 colunas, componentes colapsam toolbars.
- **Mobile (≤767px):** lista vertical com cards empilhados, gráficos com zoom horizontal por gesto.

## Próximos Passos
1. Criar wireframes de alta fidelidade no Figma seguindo estrutura descrita.
2. Prototipar fluxos críticos (upload, aplicação de filtros, detalhamento de indicadores).
3. Validar com 5 usuários-alvo (1 contador, 2 analistas, 2 gestores) e refinar microcopy e priorização de KPIs.
4. Definir biblioteca de componentes (Design System) com tokens de cor, tipografia e padrões de interação.

