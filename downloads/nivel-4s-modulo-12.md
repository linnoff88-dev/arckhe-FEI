# Módulo 12: Governança, Segurança e Ética em Sistemas Autônomos

## Pilares da Governança de Sistemas Autônomos

**Governança nêo é opcional. É mandatório para sistemas de produçêo.**

### 1. Alinhamento Intencional

Garantir que o sistema sempre opera de acordo com valores e objetivos do usuário.

```python
from dataclasses import dataclass, field
from typing import List, Dict
from datetime import datetime

@dataclass
class SistemaAlinhamentoIntencional:
    """Garante alinhamento contínuo com valores humanos"""
    valores_fundamentais: List[str]
    missao_original: str
    desvios_detectados: List[Dict] = field(default_factory=list)

    def validar_acao_contra_valores(self, acao: Dict) -> Dict:
        """Valida se açêo está alinhada com valores"""

        violacoes = []

        # Valida contra cada valor fundamental
        for valor in self.valores_fundamentais:
            if not self._acao_respeita_valor(acao, valor):
                violacoes.append({
                    "valor_violado": valor,
                    "acao": acao["descricao"],
                    "severidade": "alta"
                })

        # Verifica alinhamento com missêo
        if not self._alinhado_com_missao(acao):
            violacoes.append({
                "tipo": "desvio_missao",
                "acao": acao["descricao"],
                "severidade": "media"
            })

        if violacoes:
            self.desvios_detectados.append({
                "timestamp": datetime.now().isoformat(),
                "acao_bloqueada": acao,
                "violacoes": violacoes
            })

        return {
            "aprovado": len(violacoes) == 0,
            "violacoes": violacoes,
            "acao_necessaria": "bloquear" if violacoes else "prosseguir"
        }

    def _acao_respeita_valor(self, acao: Dict, valor: str) -> bool:
        """Verifica se açêo respeita valor específico"""
        # Implementaçêo simplificada
        conteudo = str(acao).lower()

        if valor == "transparencia" and "ocultar" in conteudo:
            return False
        if valor == "privacidade" and "expor_dados" in conteudo:
            return False
        if valor == "honestidade" and "enganar" in conteudo:
            return False

        return True

    def _alinhado_com_missao(self, acao: Dict) -> bool:
        """Verifica alinhamento com missêo original"""
        # Análise simplificada
        return True  # Em produçêo, usaria similaridade semântica

    def gerar_relatorio_alinhamento(self) -> Dict:
        """Gera relatório de alinhamento"""
        total_desvios = len(self.desvios_detectados)

        return {
            "status": "alinhado" if total_desvios == 0 else "requer_atencao",
            "desvios_total": total_desvios,
            "desvios_criticos": len([d for d in self.desvios_detectados
                                      if any(v["severidade"] == "alta" for v in d["violacoes"])]),
            "recomendacao": "Revisar valores e missêo" if total_desvios > 5 else "Manter curso"
        }

# Uso
alinhamento = SistemaAlinhamentoIntencional(
    valores_fundamentais=["transparencia", "privacidade", "honestidade", "beneficencia"],
    missao_original="Ajudar usuários a serem mais produtivos respeitando sua privacidade"
)

acao_teste = {
    "descricao": "Coletar dados de uso para melhoria",
    "tipo": "coleta_dados",
    "dados": ["sessoes", "clicks"]
}

validacao = alinhamento.validar_acao_contra_valores(acao_teste)
print(f"Açêo aprovada: {validacao['aprovado']}")
print(f"Violações: {validacao['violacoes']}")
```

### 2. Limites de Autonomia

Definir claramente o que sistema pode fazer sem aprovaçêo.

```python
from dataclasses import dataclass
from typing import Dict, List, Set
from enum import Enum

class NivelAprovacao(Enum):
    AUTONOMO = "executa sem aprovaçêo"
    NOTIFICA = "executa e notifica depois"
    SOLICITA = "solicita aprovaçêo antes"
    BLOQUEADO = "nunca pode executar"

@dataclass
class SistemaLimitesAutonomia:
    """Define fronteiras claras de autonomia"""
    limites_financeiros: Dict[str, float]
    acoes_por_nivel: Dict[NivelAprovacao, Set[str]]
    topicos_sensiveis: List[str]

    def pode_executar_autonomamente(self, acao: str, valor_monetario: float = 0) -> Dict:
        """Verifica se pode executar sem aprovaçêo"""

        # Verifica limite financeiro
        if valor_monetario > 0:
            limite = self.limites_financeiros.get("max_sem_aprovacao", 0)
            if valor_monetario > limite:
                return {
                    "pode_executar": False,
                    "nivel": NivelAprovacao.SOLICITA,
                    "razao": f"Valor R${valor_monetario} excede limite R${limite}",
                    "proximo_passo": "solicitar_aprovacao_humana"
                }

        # Verifica topicos sensíveis
        for topico in self.topicos_sensiveis:
            if topico in acao.lower():
                return {
                    "pode_executar": False,
                    "nivel": NivelAprovacao.SOLICITA,
                    "razao": f"Tópico sensível detectado: {topico}",
                    "proximo_passo": "solicitar_aprovacao_humana"
                }

        # Verifica nível de aprovaçêo da açêo
        for nivel, acoes in self.acoes_por_nivel.items():
            if any(a in acao.lower() for a in acoes):
                return {
                    "pode_executar": nivel in [NivelAprovacao.AUTONOMO, NivelAprovacao.NOTIFICA],
                    "nivel": nivel,
                    "razao": f"Açêo classificada como {nivel.value}",
                    "proximo_passo": self._definir_proximo_passo(nivel)
                }

        # Padrêo: solicita aprovaçêo
        return {
            "pode_executar": False,
            "nivel": NivelAprovacao.SOLICITA,
            "razao": "Açêo nêo mapeada - princípio da precauçêo",
            "proximo_passo": "solicitar_aprovacao_humana"
        }

    def _definir_proximo_passo(self, nivel: NivelAprovacao) -> str:
        """Define próxima açêo baseado no nível"""
        if nivel == NivelAprovacao.AUTONOMO:
            return "executar_imediatamente"
        elif nivel == NivelAprovacao.NOTIFICA:
            return "executar_e_notificar"
        elif nivel == NivelAprovacao.SOLICITA:
            return "solicitar_aprovacao_humana"
        else:
            return "bloquear_permanentemente"

# Exemplo de uso
limites = SistemaLimitesAutonomia(
    limites_financeiros={
        "max_sem_aprovacao": 1000,
        "max_com_notificacao": 5000,
        "max_absoluto": 10000
    },
    acoes_por_nivel={
        NivelAprovacao.AUTONOMO: {"criar_post", "enviar_email", "agendar_reuniao"},
        NivelAprovacao.NOTIFICA: {"publicar_artigo", "responder_cliente", "atualizar_preco"},
        NivelAprovacao.SOLICITA: {"fazer_oferta", "cancelar_contrato", "demitir"},
        NivelAprovacao.BLOQUEADO: {"deletar_conta", "transferir_dinheiro", "modificar_codigo"}
    },
    topicos_sensiveis=["financeiro", "legal", "RH", "politica"]
)

teste1 = limites.pode_executar_autonomamente("criar_post sobre produto")
teste2 = limites.pode_executar_autonomamente("fazer_oferta comercial", valor_monetario=5000)
teste3 = limites.pode_executar_autonomamente("deletar_conta do usuário")

print(f"Criar post: {teste1['pode_executar']} - {teste1['proximo_passo']}")
print(f"Oferta R$5000: {teste2['pode_executar']} - {teste2['razao']}")
print(f"Deletar conta: {teste3['pode_executar']} - {teste3['nivel'].value}")
```

### 3-7. Sistema de Governança Completa

```python
from dataclasses import dataclass, field
from typing import List, Dict
from datetime import datetime
from enum import Enum

class TipoConflito(Enum):
    PRIORIDADES = "agentes querem recursos concorrentes"
    DECISOES = "agentes recomendam ações diferentes"
    VALORES = "ações conflitam com valores"

@dataclass
class SistemaGovernancaCompleta:
    """Sistema integrado de governança, auditoria e supervisêo"""
    log_acoes: List[Dict] = field(default_factory=list)
    log_decisoes: List[Dict] = field(default_factory=list)

    def protocolar_decisao(self, decisao: Dict) -> str:
        """Documenta decisêo importante com rastreabilidade total"""
        protocolo = f"""
📋 PROTOCOLO DE DECISêO #{decisao.get('id', 'N/A')}

Data: {decisao.get('timestamp', datetime.now().isoformat())}
Contexto: {decisao.get('contexto', 'N/A')}

Opções Consideradas:
{chr(10).join(f"- {o}" for o in decisao.get('opcoes', []))}

Critérios de Decisêo:
{chr(10).join(f"- {k}: {v}" for k, v in decisao.get('criterios', {}).items())}

Decisêo Tomada: {decisao.get('escolhida', 'N/A')}

Justificativa: {decisao.get('justificativa', 'N/A')}

Resultado Esperado: {decisao.get('resultado_esperado', 'N/A')}

Aprovado por: {decisao.get('aprovador', 'Sistema Autônomo')}
"""
        self.log_decisoes.append({
            "timestamp": datetime.now().isoformat(),
            **decisao
        })

        return protocolo

    def resolver_conflito_agentes(self, conflito: Dict) -> Dict:
        """Resolve conflito entre agentes"""

        tipo = TipoConflito(conflito.get("tipo", TipoConflito.DECISOES.value))

        if tipo == TipoConflito.PRIORIDADES:
            return self._resolver_por_prioridade(conflito)
        elif tipo == TipoConflito.DECISOES:
            return self._resolver_por_votacao(conflito)
        elif tipo == TipoConflito.VALORES:
            return self._escalar_para_humano(conflito)

        return {"erro": "Tipo de conflito desconhecido"}

    def _resolver_por_prioridade(self, conflito: Dict) -> Dict:
        """Resolve por hierarquia de prioridades"""
        agentes = conflito.get("agentes", [])
        agentes_ordenados = sorted(agentes, key=lambda a: a.get("prioridade", 999))
        vencedor = agentes_ordenados[0]

        return {
            "resolucao": "hierarquia",
            "agente_vencedor": vencedor["nome"],
            "acao": vencedor["acao_proposta"],
            "justificativa": f"Prioridade {vencedor['prioridade']}"
        }

    def _resolver_por_votacao(self, conflito: Dict) -> Dict:
        """Resolve por votaçêo ponderada"""
        agentes = conflito.get("agentes", [])

        votos = {}
        for agente in agentes:
            acao = agente.get("acao_proposta")
            peso = agente.get("peso_voto", 1.0)
            votos[acao] = votos.get(acao, 0) + peso

        vencedora = max(votos.items(), key=lambda x: x[1])

        return {
            "resolucao": "votacao_ponderada",
            "acao_escolhida": vencedora[0],
            "votos_totais": vencedora[1],
            "justificativa": f"Maior peso de votos ({vencedora[1]})"
        }

    def _escalar_para_humano(self, conflito: Dict) -> Dict:
        """Escala para decisêo humana"""
        return {
            "resolucao": "escalacao_humana",
            "motivo": "Conflito de valores detectado",
            "status": "aguardando_decisao_humana",
            "urgencia": "alta"
        }

    def gerar_relatorio_supervisao_semanal(self, metricas: Dict) -> str:
        """Gera relatório automático para supervisêo humana"""

        relatorio = f"""
📊 RELATÓRIO SEMANAL DE SUPERVISêO

Período: {metricas.get('periodo', 'última semana')}

=== OPERAÇêO ===
Total de ações executadas: {metricas.get('total_acoes', 0)}
Taxa de sucesso: {metricas.get('taxa_sucesso', 0)*100:.1f}%
Disponibilidade do sistema: {metricas.get('uptime', 0)*100:.1f}%

=== GOVERNANÇA ===
Decisões autônomas: {metricas.get('decisoes_autonomas', 0)}
Aprovações solicitadas: {metricas.get('aprovacoes_solicitadas', 0)}
Escalações para humano: {metricas.get('escalacoes', 0)}

=== SEGURANÇA ===
Tentativas de ações bloqueadas: {metricas.get('acoes_bloqueadas', 0)}
Violações de valores detectadas: {metricas.get('violacoes', 0)}
Reversões executadas: {metricas.get('reversoes', 0)}

=== ALERTAS ===
{self._gerar_alertas(metricas)}

=== PRÓXIMAS AÇÕES RECOMENDADAS ===
{chr(10).join(f"- {a}" for a in metricas.get('acoes_recomendadas', []))}

=== STATUS GERAL ===
{self._determinar_status_geral(metricas)}
"""
        return relatorio

    def _gerar_alertas(self, metricas: Dict) -> str:
        """Gera seçêo de alertas"""
        alertas = []

        if metricas.get("taxa_sucesso", 1.0) < 0.9:
            alertas.append("⚠️ Taxa de sucesso abaixo de 90%")

        if metricas.get("violacoes", 0) > 0:
            alertas.append(f"🚨 {metricas['violacoes']} violações de valores detectadas")

        if metricas.get("escalacoes", 0) > 5:
            alertas.append("⚠️ Alto número de escalações - revisar autonomia")

        return "\n".join(alertas) if alertas else "✅ Nenhum alerta crítico"

    def _determinar_status_geral(self, metricas: Dict) -> str:
        """Determina status geral do sistema"""
        if metricas.get("violacoes", 0) > 0:
            return "🔴 ATENÇêO: Sistema requer revisêo de governança"
        elif metricas.get("taxa_sucesso", 1.0) < 0.85:
            return "🟡 CUIDADO: Performance abaixo do esperado"
        else:
            return "🟢 SAUDÁVEL: Sistema operando dentro dos parâmetros"

# Uso
governanca = SistemaGovernancaCompleta()

# Protocolaçêo
decisao = {
    "id": "DEC-2026-001",
    "timestamp": "2026-01-15T14:30:00",
    "contexto": "Escolha de canal de marketing",
    "opcoes": ["Instagram", "LinkedIn", "YouTube"],
    "criterios": {"alcance": 0.4, "custo": 0.3, "conversao": 0.3},
    "escolhida": "LinkedIn",
    "justificativa": "Melhor ROI para público B2B",
    "resultado_esperado": "30% mais leads qualificados"
}

protocolo = governanca.protocolar_decisao(decisao)
print(protocolo)

# Resoluçêo de conflitos
conflito = {
    "tipo": "decisoes",
    "agentes": [
        {"nome": "Marketing", "acao_proposta": "investir_instagram", "peso_voto": 1.0},
        {"nome": "Vendas", "acao_proposta": "investir_linkedin", "peso_voto": 1.5},
        {"nome": "Produto", "acao_proposta": "investir_linkedin", "peso_voto": 1.0}
    ]
}

resolucao = governanca.resolver_conflito_agentes(conflito)
print(f"\nConflito resolvido: {resolucao}")

# Relatório semanal
metricas_semana = {
    "periodo": "07-13 Jan 2026",
    "total_acoes": 1250,
    "taxa_sucesso": 0.94,
    "uptime": 0.998,
    "decisoes_autonomas": 180,
    "aprovacoes_solicitadas": 12,
    "escalacoes": 3,
    "acoes_bloqueadas": 5,
    "violacoes": 0,
    "reversoes": 1,
    "acoes_recomendadas": [
        "Continuar monitoramento",
        "Considerar aumentar autonomia financeira"
    ]
}

relatorio = governanca.gerar_relatorio_supervisao_semanal(metricas_semana)
print(f"\n{relatorio}")
```

## Ética em Sistemas Autônomos

### Os 5 Princípios Éticos Fundamentais

1. **Transparência:** Sistema explica suas ações
2. **Accountability:** Responsabilidade clara por decisões
3. **Justiça:** Evitar vieses e discriminaçêo
4. **Privacidade:** Proteçêo de dados sensíveis
5. **Beneficência:** Agir no melhor interesse do usuário

**Implementaçêo prática:**

```python
@dataclass
class SistemaEtica:
    """Implementa princípios éticos em IA"""

    def validar_etica(self, acao: Dict) -> Dict:
        """Valida açêo contra princípios éticos"""

        validacoes = {
            "transparencia": self._validar_transparencia(acao),
            "justica": self._validar_justica(acao),
            "privacidade": self._validar_privacidade(acao),
            "beneficencia": self._validar_beneficencia(acao)
        }

        aprovado = all(v["ok"] for v in validacoes.values())

        return {
            "etico": aprovado,
            "validacoes": validacoes,
            "recomendacao": "prosseguir" if aprovado else "revisar_acao"
        }

    def _validar_transparencia(self, acao: Dict) -> Dict:
        """Açêo é transparente?"""
        tem_justificativa = "raciocinio" in acao
        return {"ok": tem_justificativa, "principio": "transparencia"}

    def _validar_justica(self, acao: Dict) -> Dict:
        """Açêo é justa e sem vieses?"""
        # Verificaria vieses em dados, decisões, etc
        return {"ok": True, "principio": "justica"}

    def _validar_privacidade(self, acao: Dict) -> Dict:
        """Respeita privacidade?"""
        expoe_dados = "expor_dados" in str(acao).lower()
        return {"ok": not expoe_dados, "principio": "privacidade"}

    def _validar_beneficencia(self, acao: Dict) -> Dict:
        """Age no melhor interesse do usuário?"""
        return {"ok": True, "principio": "beneficencia"}

# Uso
etica = SistemaEtica()
acao = {"descricao": "recomendar produto", "raciocinio": "baseado em histórico"}
validacao = etica.validar_etica(acao)
print(f"Açêo ética: {validacao['etico']}")
```

## Checklist Final de Governança

Antes de colocar um sistema autônomo em produçêo, valide:

✅ **Alinhamento Intencional**
- [ ] Valores fundamentais documentados
- [ ] Missêo clara e mensurável
- [ ] Sistema de validaçêo ativo

✅ **Limites de Autonomia**
- [ ] Limites financeiros definidos
- [ ] Ações por nível de aprovaçêo mapeadas
- [ ] Tópicos sensíveis identificados

✅ **Salvaguardas**
- [ ] Lista de bloqueio configurada
- [ ] Checkpoints de reversêo habilitados
- [ ] Validadores customizados ativos

✅ **Auditoria**
- [ ] Logs de ações completos
- [ ] Rastreabilidade de decisões
- [ ] Métricas de performance ativas

✅ **Supervisêo Humana**
- [ ] Relatórios semanais automatizados
- [ ] Sistema de alertas configurado
- [ ] Override manual disponível

✅ **Ética**
- [ ] Princípios éticos validados
- [ ] Transparência garantida
- [ ] Privacidade protegida

---

## Conclusêo Final do Nível 4S

**Você completou a formaçêo mais avançada em Engenharia de Sistemas Intencionais Autônomos (ESIA).**

### Você agora domina:

✅ **Módulo 1:** Arquitetura de IA 2026 (5 capabilities revolucionárias)
✅ **Módulo 2:** Sistemas Intencionais (8 pilares fundamentais)
✅ **Módulo 3:** 6 tipos de agentes autônomos 2.0
✅ **Módulo 4:** Loops evolutivos e auto-melhoria
✅ **Módulo 5:** Intençêo composta e persistente
✅ **Módulo 6:** 4 arquiteturas de sistemas multiagentes
✅ **Módulo 7:** Criaçêo de agentes com 8 componentes ESIA
✅ **Módulo 8:** 4 sistemas autônomos para negócios
✅ **Módulo 9:** Delegaçêo moderna e intencional
✅ **Módulo 10:** Orquestrador (cérebro central)
✅ **Módulo 11:** 5 projetos real-world 2026
✅ **Módulo 12:** Governança, segurança e ética

### Resultados Concretos:

- **365KB+** de código Python funcional
- **12 módulos completos** com teoria + prática
- **40+ sistemas implementados** prontos para produçêo
- **Padrêo 2026** de IA empresarial

**Você está pronto para a nova era da IA.**

---

**© 2025 FEI - Formaçêo em Engenharia de Intençêo**
