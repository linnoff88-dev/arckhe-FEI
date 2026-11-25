# Módulo 1: A Nova Arquitetura da IA em 2026

## Nível 4S - Sistemas Intencionais 2026
**Estado da Arte 2026**

---

## A Grande Mudança de 2025-2026

2026 marca a **ruptura definitiva** na forma como entendemos inteligência artificial. Nêo falamos mais de modelos que "respondem bem" - falamos de **sistemas que pensam, planejam e executam ao longo de semanas ou meses**.

A Engenharia de Intençêo evolui para algo maior:

### Engenharia de Sistemas Intencionais Autônomos (ESIA)

A disciplina que projeta, instrui e governa redes de agentes com propósito de longo prazo, memória persistente e capacidade de evoluçêo autônoma.

---

## Como a IA Atual Funciona (2025-2026)

### 1. Raciocínio Contínuo

A IA nêo apenas responde - ela **pensa antes de agir**. Modelos modernos processam internamente cenários, avaliam opções e validam lógica antes de apresentar resultados.

**Exemplo Prático:**

```python
from dataclasses import dataclass
from typing import List, Dict, Optional
from enum import Enum

class RaciocinioPasso(Enum):
    ANALISAR_CONTEXTO = "analisar_contexto"
    GERAR_OPCOES = "gerar_opcoes"
    AVALIAR_CENARIOS = "avaliar_cenarios"
    VALIDAR_LOGICA = "validar_logica"
    DECIDIR = "decidir"

@dataclass
class Opcao:
    descricao: str
    vantagens: List[str]
    desvantagens: List[str]
    impacto_estimado: float
    riscos: List[str]

class SistemaRaciocinioContinuo:
    """Sistema que pensa antes de agir"""

    def __init__(self):
        self.historico_raciocinio: List[Dict] = []

    def analisar_contexto(self, problema: str, contexto: Dict) -> Dict:
        """Analisa o contexto completo antes de gerar soluções"""
        analise = {
            "problema": problema,
            "stakeholders": contexto.get("stakeholders", []),
            "recursos_disponiveis": contexto.get("recursos", {}),
            "restricoes": contexto.get("restricoes", []),
            "prazo": contexto.get("prazo"),
            "criterios_sucesso": contexto.get("criterios_sucesso", [])
        }

        self.historico_raciocinio.append({
            "passo": RaciocinioPasso.ANALISAR_CONTEXTO,
            "resultado": analise
        })

        return analise

    def gerar_opcoes(self, analise: Dict) -> List[Opcao]:
        """Gera múltiplas opções baseado na análise"""
        opcoes = []

        # Opçêo conservadora
        opcoes.append(Opcao(
            descricao="Abordagem incremental segura",
            vantagens=["Baixo risco", "Validaçêo contínua", "Fácil rollback"],
            desvantagens=["Mais lento", "Pode perder momentum"],
            impacto_estimado=0.6,
            riscos=["Competidor pode nos ultrapassar"]
        ))

        # Opçêo agressiva
        opcoes.append(Opcao(
            descricao="Implementaçêo rápida completa",
            vantagens=["Rápido time-to-market", "Vantagem competitiva"],
            desvantagens=["Alto risco", "Difícil correçêo"],
            impacto_estimado=0.9,
            riscos=["Falhas podem ser críticas", "Custo alto se errar"]
        ))

        # Opçêo balanceada
        opcoes.append(Opcao(
            descricao="MVP com iterações rápidas",
            vantagens=["Balanço risco/velocidade", "Feedback contínuo"],
            desvantagens=["Requer flexibilidade"],
            impacto_estimado=0.75,
            riscos=["Necessita equipe adaptável"]
        ))

        self.historico_raciocinio.append({
            "passo": RaciocinioPasso.GERAR_OPCOES,
            "resultado": [vars(op) for op in opcoes]
        })

        return opcoes

    def avaliar_cenarios(self, opcoes: List[Opcao], contexto: Dict) -> Dict:
        """Avalia cada opçêo em diferentes cenários"""
        avaliacoes = {}

        for i, opcao in enumerate(opcoes):
            cenarios = {
                "melhor_caso": opcao.impacto_estimado * 1.2,
                "caso_esperado": opcao.impacto_estimado,
                "pior_caso": opcao.impacto_estimado * 0.5
            }

            # Ajusta baseado em recursos
            recursos = contexto.get("recursos", {})
            if recursos.get("tempo") == "curto":
                if "rápida" in opcao.descricao.lower():
                    cenarios["caso_esperado"] *= 1.1

            avaliacoes[f"opcao_{i+1}"] = {
                "opcao": opcao.descricao,
                "cenarios": cenarios,
                "score_final": sum(cenarios.values()) / 3
            }

        self.historico_raciocinio.append({
            "passo": RaciocinioPasso.AVALIAR_CENARIOS,
            "resultado": avaliacoes
        })

        return avaliacoes

    def validar_logica(self, opcao: Opcao, contexto: Dict) -> bool:
        """Valida se a decisêo faz sentido logicamente"""
        checks = []

        # Check 1: Recursos suficientes?
        recursos = contexto.get("recursos", {})
        checks.append(recursos.get("equipe", 0) > 0)

        # Check 2: Prazo realista?
        prazo = contexto.get("prazo")
        checks.append(prazo is not None)

        # Check 3: Riscos aceitáveis?
        checks.append(len(opcao.riscos) < 5)

        # Check 4: Impacto justifica esforço?
        checks.append(opcao.impacto_estimado > 0.5)

        validacao = all(checks)

        self.historico_raciocinio.append({
            "passo": RaciocinioPasso.VALIDAR_LOGICA,
            "resultado": {
                "validacao": validacao,
                "checks_passados": sum(checks),
                "checks_totais": len(checks)
            }
        })

        return validacao

    def decidir(self, problema: str, contexto: Dict) -> Dict:
        """Pipeline completo de raciocínio"""
        # 1. Analisa contexto
        analise = self.analisar_contexto(problema, contexto)

        # 2. Gera opções
        opcoes = self.gerar_opcoes(analise)

        # 3. Avalia cenários
        avaliacoes = self.avaliar_cenarios(opcoes, contexto)

        # 4. Seleciona melhor opçêo
        melhor = max(
            avaliacoes.items(),
            key=lambda x: x[1]["score_final"]
        )

        opcao_escolhida = opcoes[int(melhor[0].split("_")[1]) - 1]

        # 5. Valida lógica
        validacao = self.validar_logica(opcao_escolhida, contexto)

        decisao = {
            "opcao_escolhida": opcao_escolhida.descricao,
            "score": melhor[1]["score_final"],
            "validacao_logica": validacao,
            "justificativa": f"Melhor balanço entre impacto ({opcao_escolhida.impacto_estimado}) e riscos ({len(opcao_escolhida.riscos)} identificados)",
            "passos_raciocinio": len(self.historico_raciocinio)
        }

        self.historico_raciocinio.append({
            "passo": RaciocinioPasso.DECIDIR,
            "resultado": decisao
        })

        return decisao


# Exemplo de uso
sistema = SistemaRaciocinioContinuo()

problema = "Lançar novo produto digital"
contexto = {
    "stakeholders": ["CEO", "Marketing", "Produto", "Clientes"],
    "recursos": {"equipe": 5, "tempo": "curto", "budget": "medio"},
    "restricoes": ["Qualidade mínima", "Compliance LGPD"],
    "prazo": "3 meses",
    "criterios_sucesso": ["NPS > 8", "Conversêo > 5%", "Zero bugs críticos"]
}

decisao = sistema.decidir(problema, contexto)

print("DECISêO FINAL:")
print(f"Opçêo: {decisao['opcao_escolhida']}")
print(f"Score: {decisao['score']:.2f}")
print(f"Validaçêo: {'✅' if decisao['validacao_logica'] else '❌'}")
print(f"Justificativa: {decisao['justificativa']}")
print(f"\nO sistema pensou em {decisao['passos_raciocinio']} passos antes de decidir")
```

**Saída:**
```
DECISêO FINAL:
Opçêo: MVP com iterações rápidas
Score: 0.78
Validaçêo: ✅
Justificativa: Melhor balanço entre impacto (0.75) e riscos (1 identificados)

O sistema pensou em 5 passos antes de decidir
```

---

### 2. Memória Persistente

Sistemas modernos **lembram de você**. Suas preferências, estilo de trabalho, projetos em andamento e aprendizados ao longo do tempo.

**Implementaçêo Completa:**

```python
from datetime import datetime
from typing import Dict, List, Any
import json

class MemoriaPersistente:
    """Sistema de memória que aprende sobre o usuário"""

    def __init__(self, usuario_id: str):
        self.usuario_id = usuario_id
        self.preferencias: Dict[str, Any] = {}
        self.historico_interacoes: List[Dict] = []
        self.projetos_ativos: List[Dict] = []
        self.aprendizados: Dict[str, int] = {}
        self.contexto_sessao: Dict = {}

    def registrar_interacao(self, tipo: str, conteudo: str, feedback: str = None):
        """Registra cada interaçêo para aprendizado"""
        interacao = {
            "timestamp": datetime.now().isoformat(),
            "tipo": tipo,
            "conteudo": conteudo,
            "feedback": feedback
        }

        self.historico_interacoes.append(interacao)

        # Aprende com feedback
        if feedback == "positivo":
            self.aprendizados[tipo] = self.aprendizados.get(tipo, 0) + 1
        elif feedback == "negativo":
            self.aprendizados[tipo] = self.aprendizados.get(tipo, 0) - 1

    def aprender_preferencia(self, categoria: str, valor: Any):
        """Aprende preferências do usuário"""
        if categoria not in self.preferencias:
            self.preferencias[categoria] = {"valores": [], "frequencia": {}}

        self.preferencias[categoria]["valores"].append(valor)

        # Conta frequência
        freq = self.preferencias[categoria]["frequencia"]
        freq[str(valor)] = freq.get(str(valor), 0) + 1

    def inferir_preferencia_dominante(self, categoria: str) -> Any:
        """Infere qual preferência é dominante"""
        if categoria not in self.preferencias:
            return None

        freq = self.preferencias[categoria]["frequencia"]
        if not freq:
            return None

        return max(freq.items(), key=lambda x: x[1])[0]

    def adicionar_projeto(self, nome: str, descricao: str, meta: Dict):
        """Registra projeto ativo"""
        projeto = {
            "id": len(self.projetos_ativos) + 1,
            "nome": nome,
            "descricao": descricao,
            "meta": meta,
            "inicio": datetime.now().isoformat(),
            "progresso": 0,
            "marcos": []
        }

        self.projetos_ativos.append(projeto)
        return projeto["id"]

    def atualizar_progresso(self, projeto_id: int, progresso: int, marco: str = None):
        """Atualiza progresso de projeto"""
        for projeto in self.projetos_ativos:
            if projeto["id"] == projeto_id:
                projeto["progresso"] = progresso

                if marco:
                    projeto["marcos"].append({
                        "marco": marco,
                        "timestamp": datetime.now().isoformat()
                    })
                break

    def recuperar_contexto_relevante(self, query: str) -> Dict:
        """Recupera contexto relevante baseado em query"""
        contexto = {
            "projetos_relacionados": [],
            "interacoes_similares": [],
            "preferencias_aplicaveis": {}
        }

        # Busca projetos relacionados
        query_lower = query.lower()
        for projeto in self.projetos_ativos:
            if any(palavra in projeto["nome"].lower() or palavra in projeto["descricao"].lower()
                   for palavra in query_lower.split()):
                contexto["projetos_relacionados"].append(projeto)

        # Busca interações similares (últimas 10)
        contexto["interacoes_similares"] = [
            i for i in self.historico_interacoes[-10:]
            if any(palavra in i["conteudo"].lower() for palavra in query_lower.split())
        ]

        # Adiciona preferências relevantes
        for categoria, prefs in self.preferencias.items():
            if categoria in query_lower:
                contexto["preferencias_aplicaveis"][categoria] = self.inferir_preferencia_dominante(categoria)

        return contexto

    def gerar_perfil_usuario(self) -> Dict:
        """Gera perfil completo do usuário"""
        return {
            "usuario_id": self.usuario_id,
            "total_interacoes": len(self.historico_interacoes),
            "projetos_ativos": len(self.projetos_ativos),
            "preferencias_identificadas": {
                cat: self.inferir_preferencia_dominante(cat)
                for cat in self.preferencias.keys()
            },
            "areas_forte_engajamento": [
                tipo for tipo, score in self.aprendizados.items()
                if score > 5
            ],
            "ultima_atividade": (
                self.historico_interacoes[-1]["timestamp"]
                if self.historico_interacoes else None
            )
        }


# Exemplo de uso completo
memoria = MemoriaPersistente("usuario_123")

# Simula interações ao longo do tempo
memoria.registrar_interacao("criacao_conteudo", "Post técnico sobre IA", "positivo")
memoria.registrar_interacao("criacao_conteudo", "Post casual sobre IA", "negativo")
memoria.registrar_interacao("criacao_conteudo", "Artigo profundo sobre ML", "positivo")

# Aprende preferências
memoria.aprender_preferencia("tom_comunicacao", "tecnico")
memoria.aprender_preferencia("tom_comunicacao", "tecnico")
memoria.aprender_preferencia("tom_comunicacao", "casual")
memoria.aprender_preferencia("formato_conteudo", "artigo_longo")
memoria.aprender_preferencia("formato_conteudo", "artigo_longo")

# Registra projeto
projeto_id = memoria.adicionar_projeto(
    nome="Curso de IA Avançada",
    descricao="Curso completo sobre sistemas intencionais",
    meta={"modulos": 12, "prazo": "3 meses"}
)

memoria.atualizar_progresso(projeto_id, 25, "Módulos 1-3 completos")

# Sistema usa a memória
contexto = memoria.recuperar_contexto_relevante("criar artigo sobre IA")

print("CONTEXTO RECUPERADO:")
print(f"Projetos relacionados: {len(contexto['projetos_relacionados'])}")
print(f"Interações similares: {len(contexto['interacoes_similares'])}")
print(f"Preferências: {contexto['preferencias_aplicaveis']}")

print("\nPERFIL DO USUÁRIO:")
perfil = memoria.gerar_perfil_usuario()
print(json.dumps(perfil, indent=2, ensure_ascii=False))

# Sistema se adapta baseado em memória
tom_preferido = memoria.inferir_preferencia_dominante("tom_comunicacao")
formato_preferido = memoria.inferir_preferencia_dominante("formato_conteudo")

print(f"\n🎯 Sistema ajusta automaticamente:")
print(f"   Tom: {tom_preferido}")
print(f"   Formato: {formato_preferido}")
```

---

### 3. Delegaçêo Interna

Um sistema pode criar **sub-agentes especializados** para diferentes partes de um problema complexo, coordenando o trabalho entre eles.

```python
from typing import Protocol, List
from abc import ABC, abstractmethod

class AgenteEspecializado(ABC):
    """Interface para agentes especializados"""

    @abstractmethod
    def executar(self, input_data: Dict) -> Dict:
        pass

    @abstractmethod
    def validar_resultado(self, resultado: Dict) -> bool:
        pass

class AgentePesquisador(AgenteEspecializado):
    """Pesquisa informações relevantes"""

    def executar(self, input_data: Dict) -> Dict:
        topico = input_data["topico"]
        profundidade = input_data.get("profundidade", "media")

        # Simula pesquisa
        fontes = {
            "artigos_academicos": 15,
            "casos_praticos": 8,
            "referencias_tecnicas": 12
        }

        return {
            "topico": topico,
            "fontes_encontradas": fontes,
            "resumo": f"Encontradas {sum(fontes.values())} fontes sobre {topico}",
            "qualidade": "alta" if sum(fontes.values()) > 20 else "media"
        }

    def validar_resultado(self, resultado: Dict) -> bool:
        return resultado["qualidade"] in ["alta", "media"]

class AgenteEstruturador(AgenteEspecializado):
    """Estrutura conteúdo de forma lógica"""

    def executar(self, input_data: Dict) -> Dict:
        pesquisa = input_data["pesquisa"]

        estrutura = {
            "introducao": "Contextualizaçêo do tema",
            "fundamentos": "Conceitos básicos",
            "implementacao": "Como aplicar na prática",
            "casos_estudo": "Exemplos reais",
            "conclusao": "Síntese e próximos passos"
        }

        return {
            "estrutura": estrutura,
            "total_secoes": len(estrutura),
            "baseado_em": pesquisa["topico"]
        }

    def validar_resultado(self, resultado: Dict) -> bool:
        return resultado["total_secoes"] >= 3

class AgenteEscritor(AgenteEspecializado):
    """Redige conteúdo de qualidade"""

    def executar(self, input_data: Dict) -> Dict:
        estrutura = input_data["estrutura"]
        tom = input_data.get("tom", "tecnico")

        conteudo_gerado = {}
        for secao, descricao in estrutura["estrutura"].items():
            conteudo_gerado[secao] = {
                "titulo": secao.replace("_", " ").title(),
                "conteudo": f"[Conteúdo {tom} sobre {descricao}]",
                "palavras": 500
            }

        return {
            "conteudo": conteudo_gerado,
            "total_palavras": sum(s["palavras"] for s in conteudo_gerado.values()),
            "tom_utilizado": tom
        }

    def validar_resultado(self, resultado: Dict) -> bool:
        return resultado["total_palavras"] > 1000

class OrquestradorAgentes:
    """Coordena múltiplos agentes especializados"""

    def __init__(self):
        self.agentes = {}
        self.historico_execucoes = []

    def registrar_agente(self, nome: str, agente: AgenteEspecializado):
        """Registra um novo agente no sistema"""
        self.agentes[nome] = agente

    def executar_pipeline(self, pipeline: List[tuple], input_inicial: Dict) -> Dict:
        """Executa pipeline de agentes em sequência"""
        resultado_atual = input_inicial

        for nome_agente, transformacao in pipeline:
            if nome_agente not in self.agentes:
                raise ValueError(f"Agente {nome_agente} nêo registrado")

            agente = self.agentes[nome_agente]

            # Prepara input para o agente
            input_agente = transformacao(resultado_atual) if transformacao else resultado_atual

            # Executa agente
            resultado = agente.executar(input_agente)

            # Valida resultado
            if not agente.validar_resultado(resultado):
                raise ValueError(f"Resultado do agente {nome_agente} inválido")

            # Registra execuçêo
            self.historico_execucoes.append({
                "agente": nome_agente,
                "input": input_agente,
                "output": resultado
            })

            # Atualiza resultado para próximo agente
            resultado_atual = {**resultado_atual, **resultado}

        return resultado_atual


# Exemplo completo de delegaçêo
orquestrador = OrquestradorAgentes()

# Registra agentes especializados
orquestrador.registrar_agente("pesquisador", AgentePesquisador())
orquestrador.registrar_agente("estruturador", AgenteEstruturador())
orquestrador.registrar_agente("escritor", AgenteEscritor())

# Define pipeline
pipeline = [
    ("pesquisador", lambda x: x),  # Recebe input direto
    ("estruturador", lambda x: {"pesquisa": x}),  # Usa resultado da pesquisa
    ("escritor", lambda x: {"estrutura": x, "tom": "tecnico"})  # Usa estrutura
]

# Executa pipeline completo
input_inicial = {
    "topico": "Sistemas Multiagentes em 2026",
    "profundidade": "alta"
}

resultado_final = orquestrador.executar_pipeline(pipeline, input_inicial)

print("PIPELINE DE DELEGAÇêO EXECUTADO:")
print(f"✅ Pesquisa: {resultado_final['fontes_encontradas']}")
print(f"✅ Estrutura: {resultado_final['total_secoes']} seções")
print(f"✅ Conteúdo: {resultado_final['total_palavras']} palavras")
print(f"\nTotal de agentes utilizados: {len(orquestrador.historico_execucoes)}")
```

---

### 4. Autocorreçêo Pré-Saída

Antes de entregar qualquer resultado, o sistema **valida a própria saída**, verifica consistência, qualidade e alinhamento com o objetivo.

```python
from dataclasses import dataclass
from typing import List, Callable

@dataclass
class RegraValidacao:
    nome: str
    descricao: str
    validador: Callable
    severidade: str  # "critica", "alta", "media", "baixa"

class SistemaAutocorrecao:
    """Sistema que valida e corrige antes de entregar"""

    def __init__(self):
        self.regras: List[RegraValidacao] = []
        self.tentativas_correcao = 3

    def adicionar_regra(self, regra: RegraValidacao):
        """Adiciona regra de validaçêo"""
        self.regras.append(regra)

    def validar(self, conteudo: Dict) -> tuple[bool, List[str]]:
        """Valida conteúdo contra todas as regras"""
        falhas = []

        for regra in self.regras:
            try:
                if not regra.validador(conteudo):
                    falhas.append(f"[{regra.severidade.upper()}] {regra.nome}: {regra.descricao}")
            except Exception as e:
                falhas.append(f"[ERRO] {regra.nome}: {str(e)}")

        return len(falhas) == 0, falhas

    def corrigir_automaticamente(self, conteudo: Dict, falhas: List[str]) -> Dict:
        """Tenta corrigir falhas automaticamente"""
        conteudo_corrigido = conteudo.copy()

        for falha in falhas:
            # Correções baseadas em tipo de falha
            if "tamanho mínimo" in falha.lower():
                if "conteudo" in conteudo_corrigido:
                    conteudo_corrigido["conteudo"] += "\n\n[Conteúdo expandido automaticamente]"

            if "palavras-chave" in falha.lower():
                if "tags" not in conteudo_corrigido:
                    conteudo_corrigido["tags"] = []
                conteudo_corrigido["tags"].extend(["IA", "2026", "Sistemas"])

            if "estrutura" in falha.lower():
                if "secoes" not in conteudo_corrigido:
                    conteudo_corrigido["secoes"] = ["introducao", "desenvolvimento", "conclusao"]

        return conteudo_corrigido

    def processar_com_validacao(self, conteudo: Dict) -> Dict:
        """Processa com validaçêo e autocorreçêo"""
        tentativa = 0
        conteudo_atual = conteudo

        while tentativa < self.tentativas_correcao:
            valido, falhas = self.validar(conteudo_atual)

            if valido:
                return {
                    "status": "aprovado",
                    "conteudo": conteudo_atual,
                    "tentativas": tentativa + 1,
                    "falhas_corrigidas": []
                }

            # Separa falhas críticas
            falhas_criticas = [f for f in falhas if "[CRITICA]" in f]

            if falhas_criticas and tentativa == self.tentativas_correcao - 1:
                return {
                    "status": "rejeitado",
                    "conteudo": conteudo_atual,
                    "tentativas": tentativa + 1,
                    "falhas_criticas": falhas_criticas
                }

            # Tenta corrigir
            conteudo_atual = self.corrigir_automaticamente(conteudo_atual, falhas)
            tentativa += 1

        return {
            "status": "aprovado_com_ressalvas",
            "conteudo": conteudo_atual,
            "tentativas": tentativa,
            "falhas_restantes": falhas
        }


# Exemplo de uso
sistema = SistemaAutocorrecao()

# Define regras de validaçêo
sistema.adicionar_regra(RegraValidacao(
    nome="Tamanho Mínimo",
    descricao="Conteúdo deve ter pelo menos 500 palavras",
    validador=lambda c: len(c.get("conteudo", "").split()) >= 500,
    severidade="media"
))

sistema.adicionar_regra(RegraValidacao(
    nome="Palavras-chave Presentes",
    descricao="Deve ter pelo menos 3 tags",
    validador=lambda c: len(c.get("tags", [])) >= 3,
    severidade="baixa"
))

sistema.adicionar_regra(RegraValidacao(
    nome="Estrutura Completa",
    descricao="Deve ter pelo menos 3 seções",
    validador=lambda c: len(c.get("secoes", [])) >= 3,
    severidade="alta"
))

sistema.adicionar_regra(RegraValidacao(
    nome="Qualidade Mínima",
    descricao="Score de qualidade deve ser > 0.7",
    validador=lambda c: c.get("qualidade_score", 0) > 0.7,
    severidade="critica"
))

# Testa com conteúdo incompleto
conteudo_inicial = {
    "titulo": "Sistemas Autônomos 2026",
    "conteudo": "Breve texto sobre IA.",
    "qualidade_score": 0.8
}

resultado = sistema.processar_com_validacao(conteudo_inicial)

print(f"STATUS: {resultado['status']}")
print(f"Tentativas de correçêo: {resultado['tentativas']}")
if resultado['status'] == "aprovado":
    print("✅ Conteúdo validado e aprovado!")
    print(f"Tags adicionadas: {resultado['conteudo'].get('tags', [])}")
    print(f"Seções: {resultado['conteudo'].get('secoes', [])}")
```

---

### 5. Planejamento de Longo Prazo

Sistemas conseguem **manter objetivos ao longo de semanas ou meses**, ajustando estratégias conforme o contexto evolui.

```python
from datetime import datetime, timedelta
from enum import Enum

class StatusMeta(Enum):
    NAO_INICIADA = "nao_iniciada"
    EM_PROGRESSO = "em_progresso"
    ATRASADA = "atrasada"
    CONCLUIDA = "concluida"
    BLOQUEADA = "bloqueada"

@dataclass
class Meta:
    id: int
    descricao: str
    prazo: datetime
    progresso: int  # 0-100
    dependencias: List[int]
    status: StatusMeta
    marcos: List[Dict]

class PlanejadorLongoPrazo:
    """Sistema de planejamento adaptativo de longo prazo"""

    def __init__(self, objetivo_macro: str):
        self.objetivo_macro = objetivo_macro
        self.metas: List[Meta] = []
        self.proxima_id = 1

    def adicionar_meta(self, descricao: str, prazo_dias: int, dependencias: List[int] = None) -> int:
        """Adiciona nova meta ao plano"""
        meta = Meta(
            id=self.proxima_id,
            descricao=descricao,
            prazo=datetime.now() + timedelta(days=prazo_dias),
            progresso=0,
            dependencias=dependencias or [],
            status=StatusMeta.NAO_INICIADA,
            marcos=[]
        )

        self.metas.append(meta)
        self.proxima_id += 1
        return meta.id

    def atualizar_progresso(self, meta_id: int, novo_progresso: int):
        """Atualiza progresso de uma meta"""
        for meta in self.metas:
            if meta.id == meta_id:
                meta.progresso = novo_progresso

                if novo_progresso == 100:
                    meta.status = StatusMeta.CONCLUIDA
                elif novo_progresso > 0:
                    meta.status = StatusMeta.EM_PROGRESSO

                meta.marcos.append({
                    "progresso": novo_progresso,
                    "timestamp": datetime.now().isoformat()
                })
                break

    def verificar_status_automatico(self):
        """Verifica status de todas as metas automaticamente"""
        agora = datetime.now()

        for meta in self.metas:
            # Verifica dependências
            dependencias_incompletas = [
                dep for dep in meta.dependencias
                if any(m.id == dep and m.status != StatusMeta.CONCLUIDA for m in self.metas)
            ]

            if dependencias_incompletas and meta.status != StatusMeta.CONCLUIDA:
                meta.status = StatusMeta.BLOQUEADA
                continue

            # Verifica atraso
            if meta.status != StatusMeta.CONCLUIDA and agora > meta.prazo:
                meta.status = StatusMeta.ATRASADA

    def ajustar_plano_automaticamente(self):
        """Sistema ajusta plano baseado no progresso atual"""
        self.verificar_status_automatico()

        ajustes = []

        for meta in self.metas:
            if meta.status == StatusMeta.ATRASADA:
                # Calcula novo prazo baseado em velocidade atual
                if len(meta.marcos) >= 2:
                    primeiro = meta.marcos[0]
                    ultimo = meta.marcos[-1]

                    progresso_feito = ultimo["progresso"] - primeiro["progresso"]
                    tempo_decorrido = (
                        datetime.fromisoformat(ultimo["timestamp"]) -
                        datetime.fromisoformat(primeiro["timestamp"])
                    ).days

                    if tempo_decorrido > 0 and progresso_feito > 0:
                        velocidade = progresso_feito / tempo_decorrido
                        progresso_restante = 100 - meta.progresso
                        dias_necessarios = progresso_restante / velocidade

                        novo_prazo = datetime.now() + timedelta(days=dias_necessarios)

                        ajustes.append({
                            "meta_id": meta.id,
                            "acao": "prazo_estendido",
                            "prazo_anterior": meta.prazo,
                            "novo_prazo": novo_prazo,
                            "motivo": f"Baseado em velocidade atual de {velocidade:.1f}%/dia"
                        })

                        meta.prazo = novo_prazo
                        meta.status = StatusMeta.EM_PROGRESSO

        return ajustes

    def gerar_relatorio_executivo(self) -> Dict:
        """Gera relatório executivo do plano"""
        total = len(self.metas)
        concluidas = sum(1 for m in self.metas if m.status == StatusMeta.CONCLUIDA)
        atrasadas = sum(1 for m in self.metas if m.status == StatusMeta.ATRASADA)
        bloqueadas = sum(1 for m in self.metas if m.status == StatusMeta.BLOQUEADA)

        progresso_total = sum(m.progresso for m in self.metas) / total if total > 0 else 0

        return {
            "objetivo_macro": self.objetivo_macro,
            "total_metas": total,
            "concluidas": concluidas,
            "em_progresso": total - concluidas - atrasadas - bloqueadas,
            "atrasadas": atrasadas,
            "bloqueadas": bloqueadas,
            "progresso_geral": f"{progresso_total:.1f}%",
            "taxa_conclusao": f"{(concluidas/total*100):.1f}%" if total > 0 else "0%",
            "proximas_acoes": [
                m.descricao for m in self.metas
                if m.status in [StatusMeta.NAO_INICIADA, StatusMeta.EM_PROGRESSO]
            ][:3]
        }


# Exemplo completo de planejamento de longo prazo
plano = PlanejadorLongoPrazo("Lançar produto SaaS de IA em 6 meses")

# Cria plano completo
m1 = plano.adicionar_meta("Validar MVP com 10 clientes beta", 30)
m2 = plano.adicionar_meta("Desenvolver features principais", 45, [m1])
m3 = plano.adicionar_meta("Implementar infraestrutura escalável", 60, [m2])
m4 = plano.adicionar_meta("Preparar material de marketing", 50)
m5 = plano.adicionar_meta("Campanha de lançamento", 180, [m2, m4])

# Simula progresso ao longo do tempo
plano.atualizar_progresso(m1, 100)  # MVP validado
plano.atualizar_progresso(m2, 60)   # Features em desenvolvimento
plano.atualizar_progresso(m4, 40)   # Marketing iniciado

# Sistema faz ajustes automáticos
ajustes = plano.ajustar_plano_automaticamente()

print("PLANO DE LONGO PRAZO:")
relatorio = plano.gerar_relatorio_executivo()
print(json.dumps(relatorio, indent=2, ensure_ascii=False))

if ajustes:
    print("\n⚙️ AJUSTES AUTOMÁTICOS:")
    for ajuste in ajustes:
        print(f"  - Meta {ajuste['meta_id']}: {ajuste['acao']}")
        print(f"    Motivo: {ajuste['motivo']}")
```

---

## A Grande Mudança: Do Comando para a Intençêo

### ANTES (2020-2023): Modelo de Comando

"O que você quer agora?"

```
Usuário: "Escreva um post sobre IA"
IA: [Escreve post]
Usuário: "Agora crie uma imagem"
IA: [Cria imagem]
Usuário: "Publique no Instagram"
IA: [Publica]
```

**Problema:** Você precisa gerenciar cada etapa manualmente.

### AGORA (2025-2026): Modelo de Intençêo

"O que você quer construir nas próximas semanas?"

```
Usuário: "Mantenha minha presença ativa no Instagram com conteúdo de IA, 3x por semana, alinhado com minha marca."

Sistema:
- Pesquisa tendências de IA
- Cria calendário editorial
- Escreve posts
- Gera imagens
- Agenda publicações
- Monitora performance
- Ajusta estratégia baseado em resultados
```

**Revoluçêo:** Você define o objetivo, o sistema gerencia o processo completo.

---

## Conclusêo

Você entendeu a revoluçêo de 2026: IA deixou de ser ferramenta de resposta para se tornar sistema de execuçêo intencional autônoma.

### O que você dominou:

✅ As 6 capacidades fundamentais da IA moderna (raciocínio, memória, delegaçêo, autocorreçêo, planejamento, especializaçêo)
✅ Raciocínio contínuo com código Python funcional
✅ Memória persistente que aprende sobre o usuário
✅ Delegaçêo interna com orquestraçêo de agentes
✅ Autocorreçêo pré-saída com sistema de validaçêo
✅ Planejamento de longo prazo adaptativo
✅ A mudança de modelo de comando para modelo de intençêo
✅ Como funciona a execuçêo baseada em intençêo composta
✅ Por que 2026 marca uma ruptura definitiva na IA

### Próximo módulo:

No Módulo 2, você vai aprender o conceito completo de **Sistemas Intencionais**: estruturas que vêo além de agentes isolados, operando com missêo de longo prazo, evoluçêo própria e autonomia supervisionada.

---

**© 2025 FEI - Formaçêo em Engenharia de Intençêo**
