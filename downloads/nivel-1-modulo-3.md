# 📘 Módulo 3: Contexto é Tudo
## Dominando contexto rico e técnicas zero-shot vs few-shot

**Nível 1: Fundamentos de Engenharia de Intenção**
**FEI - Formação em Engenharia de Intenção**

---

## 🎯 Introdução

Se os 7 pilares são a arquitetura, **contexto é o combustível** que faz a IA funcionar em alto nível. Mas nem todo contexto é igual — existe uma arte em fornecer a quantidade certa, da forma certa, no momento certo.

Neste módulo, você aprenderá as técnicas modernas de contextualização: **zero-shot** (sem exemplos), **few-shot** (com exemplos) e como escolher entre elas.

### 💡 Por que contexto domina em 2025?

Modelos modernos têm **janelas de contexto gigantes** (Claude 3.7: 200k tokens, GPT-4: 128k tokens). Isso significa que você pode fornecer documentação completa, exemplos detalhados e histórico extenso — mas precisa saber COMO fazer isso estrategicamente.

---

## 🚀 A Revolução do Contexto (2022 → 2025)

### ❌ 2022: Era da Escassez

- **Limite:** 4k tokens (~3.000 palavras)
- **Estratégia:** Contexto mínimo, super comprimido
- **Problema:** IA não tinha informação suficiente
- **Solução:** Truques e atalhos para economizar tokens

### ✅ 2025: Era da Abundância

- **Limite:** 200k tokens (~150.000 palavras = 300 páginas)
- **Estratégia:** Contexto rico, estruturado e relevante
- **Oportunidade:** IA tem contexto suficiente para decisões complexas
- **Desafio:** Organizar contexto para não gerar ruído

### 🎓 Insight Fundamental

O jogo mudou de **"economizar contexto"** para **"estruturar contexto"**. Não é sobre dar menos — é sobre dar MELHOR.

---

## 🎯 Zero-Shot vs Few-Shot: Quando Usar Cada Um

### 🔵 Zero-Shot: Sem Exemplos

Você descreve o que quer SEM fornecer exemplos. A IA usa seu conhecimento geral para inferir o padrão.

**✅ Quando usar Zero-Shot:**
- Tarefas comuns e bem conhecidas
- Quando você quer flexibilidade criativa
- Formato de saída é padrão (resumo, lista, artigo)
- Você não tem exemplos prontos

**📝 Exemplo Zero-Shot:**
```
Analise o sentimento destes reviews de clientes e classifique como:
- Positivo
- Neutro
- Negativo

Explique brevemente o motivo da classificação.

Reviews:
1. "Produto chegou rápido mas veio com defeito"
2. "Excelente qualidade, recomendo!"
3. "Não atendeu expectativas"
```

---

### 🟣 Few-Shot: Com Exemplos

Você fornece **2-5 exemplos** do que quer, e a IA aprende o padrão específico que você busca.

**✅ Quando usar Few-Shot:**
- Formato de saída muito específico
- Tom ou estilo particular
- Padrão personalizado da sua empresa
- Tarefa ambígua sem exemplos
- Quando zero-shot não funcionou bem

**📝 Exemplo Few-Shot:**
```
Converta descrições de produtos em títulos otimizados para SEO
seguindo este padrão:

EXEMPLO 1:
Descrição: "Tênis esportivo para corrida com amortecimento"
Título: "Tênis Corrida Profissional | Amortecimento Premium | Frete Grátis"

EXEMPLO 2:
Descrição: "Fone bluetooth com cancelamento de ruído"
Título: "Fone Bluetooth Premium | Cancelamento de Ruído | 30h Bateria"

EXEMPLO 3:
Descrição: "Cadeira ergonômica para escritório"
Título: "Cadeira Ergonômica Office | Suporte Lombar | Até 150kg"

Agora faça para:
"Mouse sem fio recarregável com alta precisão"
```

---

### ⚖️ Comparação Direta

| Critério | Zero-Shot | Few-Shot |
|----------|-----------|----------|
| **Precisão** | Boa para tarefas comuns | Excelente para padrões específicos |
| **Velocidade** | Rápido (menos contexto) | Mais lento (mais contexto) |
| **Flexibilidade** | Alta (IA decide) | Baixa (segue exemplos) |
| **Esforço** | Mínimo | Moderado (criar exemplos) |

---

## 📊 Hierarquia do Contexto: O que Incluir e em Que Ordem

A **ordem do contexto importa**. IAs dão mais peso para informações no início e no final (efeito primacy e recency). Use esta hierarquia:

### 1. Objetivo e Intenção
SEMPRE no topo. A IA precisa saber O QUE você quer e POR QUÊ antes de qualquer outra coisa.

### 2. Contexto Situacional
Cenário, audiência, restrições. O "ambiente" onde a tarefa se insere.

### 3. Exemplos (se Few-Shot)
2-5 exemplos bem escolhidos que demonstram o padrão esperado.

### 4. Dados ou Materiais
O conteúdo bruto que a IA processará (texto, código, planilhas, etc).

### 5. Critérios e Formato
No final, reforce COMO você quer a resposta. A IA revisitará isso antes de gerar.

**⚡ Dica Pro:**
Use **seções com títulos** para organizar contexto longo:
```
## OBJETIVO:
## CONTEXTO:
## MATERIAIS:
```
Isso facilita para você E para a IA.

---

## 🛠️ Técnicas Práticas de Contextualização

### 1. Context Bracketing
Use delimitadores visuais para separar contexto de instrução:

```
===== CONTEXTO =====
Somos uma startup B2B SaaS...

===== TAREFA =====
Crie uma estratégia de marketing...
```

---

### 2. Contexto em Camadas
Forneça contexto em níveis de detalhe crescente:

```
Camada 1 (Essencial): Startup de fintech, 50 funcionários
Camada 2 (Relevante): Produto B2B para PMEs, ARR $2M
Camada 3 (Detalhe): Stack: React, Node, AWS, time distribuído
```

---

### 3. Anti-Contexto (O que NÃO fazer)
Às vezes é mais fácil dizer o que evitar:

```
Critérios:
✓ Linguagem simples
✓ Exemplos práticos
✗ NÃO use jargão técnico
✗ NÃO seja genérico ou vago
```

---

### 4. Contexto Progressivo
Em conversas longas, adicione contexto conforme necessário:

```
Mensagem 1: Objetivo geral
Mensagem 2: Adiciona detalhes técnicos
Mensagem 3: Refina com feedback
Mensagem 4: Ajusta final com novo contexto
```

---

## ✍️ Exercícios Práticos

### Exercício 1: Zero-Shot vs Few-Shot
**Nível:** Iniciante

Para cada tarefa, decida se você usaria zero-shot ou few-shot, e por quê:

1. Escrever um email de agradecimento formal
2. Converter nomes de produtos em códigos SKU seguindo padrão interno
3. Resumir um artigo científico
4. Gerar posts para Instagram com tom da sua marca específica

---

### Exercício 2: Hierarquia do Contexto
**Nível:** Intermediário

Este contexto está bagunçado. Reorganize seguindo a hierarquia correta:

```
"Aqui está a planilha de vendas. Quero um relatório executivo.
Somos uma loja online de eletrônicos. As vendas caíram 20%.
Preciso apresentar para diretoria sexta-feira. Use gráficos e seja direto."
```

---

### Exercício 3: Few-Shot na Prática
**Nível:** Avançado

**Cenário:** Você precisa que a IA converta descrições técnicas em linguagem acessível para clientes leigos.

**🎯 Desafio:** Crie uma solicitação few-shot com 3 exemplos bem escolhidos que ensinem a IA a fazer isso perfeitamente.

---

## ⚡ Poder do Contexto: Transformação Real

### ❌ Contexto Pobre
```
"Analise esses dados de vendas e me dê insights"
```
**Resultado:** Insights genéricos tipo "as vendas variam ao longo do tempo"

### ✅ Contexto Rico
```
OBJETIVO: Identificar por que vendas caíram 30%
CONTEXTO: E-commerce moda, público 25-40 anos, Brasil
PERÍODO: Comparar Q3-2024 vs Q3-2023
FOCO: Abandono de carrinho, canais de aquisição, produtos
FORMATO: Top 3 causas + ações corretivas prioritárias
```
**Resultado:** Análise cirúrgica com ações específicas implementáveis!

---

## 🎓 Conclusão do Módulo

Você dominou a arte do **contexto estratégico**. Agora sabe quando usar zero-shot, quando usar few-shot, e como estruturar contexto para máxima eficácia.

### ✅ O que você dominou:
- Diferença fundamental entre zero-shot e few-shot
- Hierarquia do contexto e ordem de apresentação
- Técnicas práticas de contextualização
- Como transformar contexto pobre em contexto rico

---

**Próximos Passos:**
Continue para o Módulo 4 para aprender sobre **Frameworks Práticos** (RACE, CRISPE, CLEAR)

---

© 2025 FEI - Formação em Engenharia de Intenção
https://inematds.github.io/FEI/
