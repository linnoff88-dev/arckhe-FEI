# Módulo 10: O "Cérebro Central" do Sistema

## Agente Orquestrador de Alto Nível

O componente mais importante de qualquer sistema intencional é o **Agente Orquestrador**.

**Enquanto outros agentes sêo especialistas, o Orquestrador é o gerente geral que coordena tudo.**

### As 8 Responsabilidades do Orquestrador

**1. Entender Intençêo Macro**
Traduz objetivos de alto nível em estratégias executáveis.

**2. Quebrar em Objetivos**
Decompõe missêo complexa em metas intermediárias e microtarefas.

**3. Distribuir Entre Agentes**
Delega tarefas para agentes especializados baseado em competências.

**4. Validar Resultados**
Verifica qualidade e alinhamento com objetivos antes de prosseguir.

**5. Corrigir Falhas**
Detecta problemas e implementa soluções ou escalona para humano.

**6. Buscar Dados Externos**
Identifica quando precisa de informações adicionais e busca autonomamente.

**7. Evoluir o Sistema**
Aprende padrões, otimiza processos e sugere melhorias.

**8. Manter Alinhamento com o Humano**
Garante que o sistema sempre opera de acordo com intençêo do usuário.

## Implementaçêo Completa do Orquestrador

```python
from dataclasses import dataclass, field
from typing import List, Dict, Optional, Callable, Any
from enum import Enum
from datetime import datetime
import json

class StatusTarefa(Enum):
    PENDENTE = "aguardando execuçêo"
    EM_PROGRESSO = "em execuçêo"
    BLOQUEADA = "bloqueada por dependência"
    CONCLUIDA = "concluída com sucesso"
    FALHA = "falhou na execuçêo"
    CANCELADA = "cancelada"

class PrioridadeTarefa(Enum):
    CRITICA = 1
    ALTA = 2
    MEDIA = 3
    BAIXA = 4

@dataclass
class Tarefa:
    """Unidade de trabalho distribuível"""
    id: str
    descricao: str
    agente_responsavel: str
    prioridade: PrioridadeTarefa
    dependencias: List[str] = field(default_factory=list)
    status: StatusTarefa = StatusTarefa.PENDENTE
    resultado: Optional[Dict] = None
    tentativas: int = 0
    max_tentativas: int = 3
    inicio: Optional[datetime] = None
    fim: Optional[datetime] = None

@dataclass
class AgenteEspecializado:
    """Agente especialista disponível para o orquestrador"""
    nome: str
    especialidades: List[str]
    capacidade_atual: float  # 0.0 a 1.0
    taxa_sucesso_historica: float  # 0.0 a 1.0
    tarefas_executadas: int = 0

@dataclass
class AgenteOrquestrador:
    """Cérebro central que coordena todos os agentes"""
    missao_macro: str
    agentes_disponiveis: List[AgenteEspecializado]
    tarefas: List[Tarefa] = field(default_factory=list)
    historico_decisoes: List[Dict] = field(default_factory=list)
    alinhamento_humano: float = 1.0  # Grau de alinhamento com intençêo humana

    def receber_intencao(self, intencao: str, contexto: Dict) -> Dict:
        """1. Recebe e entende intençêo de alto nível"""
        self.missao_macro = intencao

        print(f"📥 Intençêo recebida: {intencao}")

        # Analisa intençêo
        analise = self._analisar_intencao(intencao, contexto)

        # Quebra em objetivos
        objetivos = self._decompo_em_objetivos(analise)

        # Cria plano de execuçêo
        plano = self._criar_plano_execucao(objetivos)

        self._registrar_decisao("receber_intencao", {
            "intencao": intencao,
            "objetivos_identificados": len(objetivos),
            "tarefas_criadas": len(plano["tarefas"])
        })

        return {
            "analise": analise,
            "objetivos": objetivos,
            "plano": plano,
            "status": "pronto_para_executar"
        }

    def _analisar_intencao(self, intencao: str, contexto: Dict) -> Dict:
        """Analisa e estrutura a intençêo"""
        return {
            "intencao_original": intencao,
            "objetivo_principal": self._extrair_objetivo_principal(intencao),
            "metricas_sucesso": self._identificar_metricas(intencao),
            "restricoes": contexto.get("restricoes", []),
            "prazo": contexto.get("prazo", None),
            "complexidade": self._estimar_complexidade(intencao)
        }

    def _extrair_objetivo_principal(self, intencao: str) -> str:
        """Extrai objetivo principal da intençêo"""
        # Em implementaçêo real, usaria LLM para análise semântica
        return intencao.split(".")[0] if "." in intencao else intencao

    def _identificar_metricas(self, intencao: str) -> List[str]:
        """Identifica métricas mencionadas na intençêo"""
        metricas_conhecidas = ["conversêo", "engajamento", "receita", "satisfaçêo", "eficiência"]
        return [m for m in metricas_conhecidas if m in intencao.lower()]

    def _estimar_complexidade(self, intencao: str) -> str:
        """Estima complexidade da tarefa"""
        palavras = len(intencao.split())
        if palavras < 10:
            return "simples"
        elif palavras < 30:
            return "média"
        else:
            return "complexa"

    def _decompo_em_objetivos(self, analise: Dict) -> List[Dict]:
        """2. Quebra missêo complexa em objetivos menores"""
        objetivo_principal = analise["objetivo_principal"]
        complexidade = analise["complexidade"]

        if complexidade == "simples":
            return [{"objetivo": objetivo_principal, "prioridade": PrioridadeTarefa.ALTA}]

        # Para tarefas complexas, decomp em etapas
        return [
            {"objetivo": "Pesquisar e reunir informações", "prioridade": PrioridadeTarefa.ALTA},
            {"objetivo": "Elaborar estratégia", "prioridade": PrioridadeTarefa.ALTA},
            {"objetivo": "Executar implementaçêo", "prioridade": PrioridadeTarefa.MEDIA},
            {"objetivo": "Validar e ajustar", "prioridade": PrioridadeTarefa.MEDIA},
            {"objetivo": "Documentar e reportar", "prioridade": PrioridadeTarefa.BAIXA}
        ]

    def _criar_plano_execucao(self, objetivos: List[Dict]) -> Dict:
        """Cria plano com tarefas e dependências"""
        tarefas_criadas = []

        for i, obj in enumerate(objetivos):
            tarefa = Tarefa(
                id=f"tarefa_{i+1}",
                descricao=obj["objetivo"],
                agente_responsavel=self._selecionar_agente_adequado(obj["objetivo"]),
                prioridade=obj["prioridade"],
                dependencias=[f"tarefa_{i}"] if i > 0 else []
            )
            tarefas_criadas.append(tarefa)
            self.tarefas.append(tarefa)

        return {
            "tarefas": [
                {
                    "id": t.id,
                    "descricao": t.descricao,
                    "agente": t.agente_responsavel,
                    "dependencias": t.dependencias
                }
                for t in tarefas_criadas
            ],
            "ordem_execucao": [t.id for t in tarefas_criadas]
        }

    def _selecionar_agente_adequado(self, tarefa_descricao: str) -> str:
        """3. Seleciona melhor agente para cada tarefa"""
        # Mapeia palavras-chave para especialidades
        if "pesquisar" in tarefa_descricao.lower() or "informações" in tarefa_descricao.lower():
            especialidade_necessaria = "pesquisa"
        elif "estratégia" in tarefa_descricao.lower() or "plano" in tarefa_descricao.lower():
            especialidade_necessaria = "estrategia"
        elif "implementar" in tarefa_descricao.lower() or "executar" in tarefa_descricao.lower():
            especialidade_necessaria = "execucao"
        elif "validar" in tarefa_descricao.lower() or "revisar" in tarefa_descricao.lower():
            especialidade_necessaria = "validacao"
        else:
            especialidade_necessaria = "geral"

        # Encontra melhor agente disponível
        candidatos = [
            a for a in self.agentes_disponiveis
            if especialidade_necessaria in a.especialidades
        ]

        if not candidatos:
            # Se nenhum especialista, pega qualquer um disponível
            candidatos = self.agentes_disponiveis

        # Seleciona por taxa de sucesso e capacidade
        melhor = max(
            candidatos,
            key=lambda a: (a.taxa_sucesso_historica, 1 - a.capacidade_atual)
        )

        return melhor.nome

    def executar_plano(self) -> Dict:
        """Executa plano orquestrando todos os agentes"""
        print(f"\n🎯 Iniciando execuçêo do plano ({len(self.tarefas)} tarefas)")

        resultados = []

        while self._tem_tarefas_pendentes():
            # Identifica tarefas prontas para executar
            tarefas_prontas = self._identificar_tarefas_prontas()

            if not tarefas_prontas:
                print("⚠️ Nenhuma tarefa pronta - verificando bloqueios")
                break

            # Executa tarefas prontas
            for tarefa in tarefas_prontas:
                resultado = self._executar_tarefa(tarefa)
                resultados.append(resultado)

                # 4. Valida resultado
                validacao = self._validar_resultado(tarefa, resultado)

                if not validacao["aprovado"]:
                    # 5. Corrige falhas
                    self._corrigir_falha(tarefa, validacao)

        return {
            "status": "concluido" if not self._tem_tarefas_pendentes() else "parcial",
            "tarefas_concluidas": len([t for t in self.tarefas if t.status == StatusTarefa.CONCLUIDA]),
            "tarefas_falhadas": len([t for t in self.tarefas if t.status == StatusTarefa.FALHA]),
            "resultados": resultados
        }

    def _tem_tarefas_pendentes(self) -> bool:
        """Verifica se ainda há tarefas para executar"""
        return any(
            t.status in [StatusTarefa.PENDENTE, StatusTarefa.EM_PROGRESSO, StatusTarefa.BLOQUEADA]
            for t in self.tarefas
        )

    def _identificar_tarefas_prontas(self) -> List[Tarefa]:
        """Identifica tarefas sem dependências bloqueantes"""
        prontas = []

        for tarefa in self.tarefas:
            if tarefa.status != StatusTarefa.PENDENTE:
                continue

            # Verifica se todas dependências foram concluídas
            dependencias_ok = all(
                self._buscar_tarefa(dep_id).status == StatusTarefa.CONCLUIDA
                for dep_id in tarefa.dependencias
            )

            if dependencias_ok:
                prontas.append(tarefa)
            else:
                tarefa.status = StatusTarefa.BLOQUEADA

        # Ordena por prioridade
        return sorted(prontas, key=lambda t: t.prioridade.value)

    def _buscar_tarefa(self, tarefa_id: str) -> Optional[Tarefa]:
        """Busca tarefa por ID"""
        for tarefa in self.tarefas:
            if tarefa.id == tarefa_id:
                return tarefa
        return None

    def _executar_tarefa(self, tarefa: Tarefa) -> Dict:
        """Executa tarefa delegando para agente especializado"""
        print(f"▶️ Executando: {tarefa.descricao} (Agente: {tarefa.agente_responsavel})")

        tarefa.status = StatusTarefa.EM_PROGRESSO
        tarefa.inicio = datetime.now()
        tarefa.tentativas += 1

        # Simula execuçêo do agente (em produçêo, delegaria de verdade)
        import random
        sucesso = random.random() > 0.2  # 80% de chance de sucesso

        if sucesso:
            resultado = {
                "sucesso": True,
                "output": f"Tarefa '{tarefa.descricao}' concluída com sucesso",
                "qualidade": random.uniform(0.7, 1.0)
            }
            tarefa.status = StatusTarefa.CONCLUIDA
        else:
            resultado = {
                "sucesso": False,
                "erro": "Falha na execuçêo",
                "qualidade": 0.0
            }
            tarefa.status = StatusTarefa.FALHA

        tarefa.fim = datetime.now()
        tarefa.resultado = resultado

        # Atualiza capacidade do agente
        agente = next(a for a in self.agentes_disponiveis if a.nome == tarefa.agente_responsavel)
        agente.tarefas_executadas += 1
        agente.capacidade_atual = min(1.0, agente.capacidade_atual + 0.1)

        return resultado

    def _validar_resultado(self, tarefa: Tarefa, resultado: Dict) -> Dict:
        """4. Valida qualidade do resultado"""
        if not resultado.get("sucesso"):
            return {
                "aprovado": False,
                "razao": "Tarefa falhou na execuçêo"
            }

        qualidade = resultado.get("qualidade", 0)
        qualidade_minima = 0.7

        if qualidade < qualidade_minima:
            return {
                "aprovado": False,
                "razao": f"Qualidade {qualidade:.2f} abaixo do mínimo {qualidade_minima}"
            }

        return {
            "aprovado": True,
            "qualidade": qualidade
        }

    def _corrigir_falha(self, tarefa: Tarefa, validacao: Dict):
        """5. Tenta corrigir falha ou escala para humano"""
        print(f"⚠️ Falha detectada: {validacao['razao']}")

        if tarefa.tentativas < tarefa.max_tentativas:
            print(f"🔄 Tentando novamente ({tarefa.tentativas}/{tarefa.max_tentativas})")
            tarefa.status = StatusTarefa.PENDENTE
            tarefa.resultado = None
        else:
            print(f"🚨 Máximo de tentativas atingido - ESCALANDO PARA HUMANO")
            self._escalar_para_humano(tarefa, validacao)

    def _escalar_para_humano(self, tarefa: Tarefa, motivo: Dict):
        """Escala problema para supervisêo humana"""
        self._registrar_decisao("escalacao_humano", {
            "tarefa_id": tarefa.id,
            "descricao": tarefa.descricao,
            "tentativas": tarefa.tentativas,
            "motivo": motivo
        })

    def _registrar_decisao(self, tipo: str, detalhes: Dict):
        """Registra decisêo para auditoria e aprendizado"""
        self.historico_decisoes.append({
            "timestamp": datetime.now().isoformat(),
            "tipo": tipo,
            "detalhes": detalhes
        })

    def buscar_dados_externos(self, necessidade: str) -> Dict:
        """6. Busca informações externas quando necessário"""
        print(f"🔍 Buscando dados externos: {necessidade}")

        # Em produçêo, integraria com APIs, bancos de dados, web scraping, etc
        return {
            "fonte": "api_externa",
            "dados": {"exemplo": "dados relevantes"},
            "timestamp": datetime.now().isoformat()
        }

    def evoluir_sistema(self) -> Dict:
        """7. Aprende e otimiza baseado em histórico"""
        if len(self.historico_decisoes) < 10:
            return {"status": "dados_insuficientes"}

        # Analisa padrões de sucesso e falha
        analise = self._analisar_padroes_historico()

        # Propõe melhorias
        melhorias = self._propor_melhorias(analise)

        return {
            "analise": analise,
            "melhorias_propostas": melhorias,
            "aplicacao": "automatica" if len(melhorias) < 3 else "requer_aprovacao"
        }

    def _analisar_padroes_historico(self) -> Dict:
        """Identifica padrões no histórico"""
        tarefas_concluidas = [t for t in self.tarefas if t.status == StatusTarefa.CONCLUIDA]
        tarefas_falhadas = [t for t in self.tarefas if t.status == StatusTarefa.FALHA]

        return {
            "taxa_sucesso_geral": len(tarefas_concluidas) / len(self.tarefas) if self.tarefas else 0,
            "agente_mais_eficaz": self._identificar_melhor_agente(),
            "tipo_tarefa_mais_falha": self._identificar_tipo_falha_comum()
        }

    def _identificar_melhor_agente(self) -> str:
        """Identifica agente com melhor performance"""
        if not self.agentes_disponiveis:
            return "nenhum"

        melhor = max(self.agentes_disponiveis, key=lambda a: a.taxa_sucesso_historica)
        return melhor.nome

    def _identificar_tipo_falha_comum(self) -> str:
        """Identifica tipo de tarefa com mais falhas"""
        # Análise simplificada
        return "tarefas_complexas"

    def _propor_melhorias(self, analise: Dict) -> List[str]:
        """Propõe melhorias baseado na análise"""
        melhorias = []

        if analise["taxa_sucesso_geral"] < 0.8:
            melhorias.append("Aumentar número de tentativas permitidas")
            melhorias.append("Adicionar validaçêo intermediária")

        if analise["tipo_tarefa_mais_falha"]:
            melhorias.append(f"Criar agente especializado em {analise['tipo_tarefa_mais_falha']}")

        return melhorias

    def verificar_alinhamento_humano(self) -> Dict:
        """8. Garante alinhamento com intençêo humana"""
        return {
            "alinhamento_atual": self.alinhamento_humano,
            "desvios_detectados": self.alinhamento_humano < 0.9,
            "acao_necessaria": "revisar_restricoes" if self.alinhamento_humano < 0.9 else "continuar"
        }

    def gerar_relatorio_executivo(self) -> str:
        """Gera relatório completo para humano"""
        total_tarefas = len(self.tarefas)
        concluidas = len([t for t in self.tarefas if t.status == StatusTarefa.CONCLUIDA])
        falhadas = len([t for t in self.tarefas if t.status == StatusTarefa.FALHA])

        relatorio = f"""
📊 RELATÓRIO EXECUTIVO DO ORQUESTRADOR

Missêo: {self.missao_macro}

📈 Progresso:
- Total de tarefas: {total_tarefas}
- Concluídas: {concluidas} ({concluidas/total_tarefas*100:.1f}%)
- Falhadas: {falhadas} ({falhadas/total_tarefas*100:.1f}%)

👥 Agentes:
{chr(10).join(f"- {a.nome}: {a.tarefas_executadas} tarefas, {a.taxa_sucesso_historica*100:.1f}% sucesso" for a in self.agentes_disponiveis)}

🎯 Alinhamento: {self.alinhamento_humano*100:.1f}%

📝 Decisões tomadas: {len(self.historico_decisoes)}
"""
        return relatorio

# Exemplo de uso completo
agentes = [
    AgenteEspecializado(
        nome="Pesquisador",
        especialidades=["pesquisa", "analise"],
        capacidade_atual=0.2,
        taxa_sucesso_historica=0.92
    ),
    AgenteEspecializado(
        nome="Estrategista",
        especialidades=["estrategia", "planejamento"],
        capacidade_atual=0.3,
        taxa_sucesso_historica=0.88
    ),
    AgenteEspecializado(
        nome="Executor",
        especialidades=["execucao", "implementacao"],
        capacidade_atual=0.5,
        taxa_sucesso_historica=0.85
    ),
    AgenteEspecializado(
        nome="Auditor",
        especialidades=["validacao", "qualidade"],
        capacidade_atual=0.1,
        taxa_sucesso_historica=0.95
    )
]

orquestrador = AgenteOrquestrador(
    missao_macro="",
    agentes_disponiveis=agentes
)

# Recebe intençêo de alto nível
intencao = """Criar campanha de marketing digital para lançamento de produto,
com foco em conversêo acima de 3% e geraçêo de 500 leads qualificados em 30 dias"""

plano = orquestrador.receber_intencao(intencao, {
    "restricoes": ["budget máximo R$10k", "sem promessas falsas"],
    "prazo": "30 dias"
})

print(f"\n{orquestrador.gerar_relatorio_executivo()}")

# Executa plano
resultado_execucao = orquestrador.executar_plano()

print(f"\n{orquestrador.gerar_relatorio_executivo()}")
```

## Padrões de Orquestraçêo

```python
from enum import Enum

class PadraoOrquestracao(Enum):
    SEQUENCIAL = "tarefas executadas uma após a outra"
    PARALELO = "tarefas independentes executadas simultaneamente"
    HIERARQUICO = "orquestrador delega para sub-orquestradores"
    ADAPTATIVO = "ajusta estratégia baseado em resultados intermediários"

@dataclass
class ConfiguracaoOrquestracao:
    """Configuraçêo do comportamento do orquestrador"""
    padrao: PadraoOrquestracao
    max_tarefas_paralelas: int = 3
    timeout_tarefa_minutos: int = 30
    auto_retry: bool = True
    escalacao_automatica: bool = True
    aprendizado_continuo: bool = True

# Orquestraçêo Sequencial: Cada tarefa após a anterior
config_sequencial = ConfiguracaoOrquestracao(
    padrao=PadraoOrquestracao.SEQUENCIAL,
    max_tarefas_paralelas=1
)

# Orquestraçêo Paralela: Máximo de tarefas simultâneas
config_paralela = ConfiguracaoOrquestracao(
    padrao=PadraoOrquestracao.PARALELO,
    max_tarefas_paralelas=5
)

# Orquestraçêo Adaptativa: Ajusta baseado em performance
config_adaptativa = ConfiguracaoOrquestracao(
    padrao=PadraoOrquestracao.ADAPTATIVO,
    max_tarefas_paralelas=3,
    aprendizado_continuo=True
)
```

## O Orquestrador É o Cérebro

**Diferença entre agente especialista e orquestrador:**

| Aspecto | Agente Especialista | Orquestrador |
|---------|---------------------|--------------|
| Foco | Tarefa específica | Coordenaçêo global |
| Escopo | Domínio restrito | Visêo macro |
| Decisões | Táticas | Estratégicas |
| Autonomia | Executa o que recebe | Decide o que fazer |
| Relacionamento | Recebe ordens | Delega tarefas |
| Aprendizado | Melhora na especialidade | Otimiza o sistema todo |

**O orquestrador pensa, os agentes executam.**

---

**© 2025 FEI - Formaçêo em Engenharia de Intençêo**
