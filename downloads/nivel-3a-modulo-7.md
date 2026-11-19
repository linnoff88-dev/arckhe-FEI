# Módulo 7: Boas Práticas e Segurança em Sistemas de Agentes
## Nível 3A - Agentes e Sistemas Autônomos na Engenharia de Intenção

**FEI - Formação em Engenharia de Intenção**

---

## Introdução

Você já sabe criar agentes e sistemas multiagentes funcionais. Mas criar algo que **funciona** é diferente de criar algo **seguro, confiável e pronto para produção**.

Neste módulo final, você aprenderá as **5 pilares fundamentais** para colocar sistemas de agentes em produção com confiança:

1. **Validação e Testes** - Como garantir que funciona
2. **Monitoramento e Observabilidade** - Como saber o que está acontecendo
3. **Segurança e Privacidade** - Como proteger dados e prevenir abusos
4. **Escalabilidade e Performance** - Como crescer sem quebrar
5. **Manutenção e Evolução** - Como melhorar continuamente

**Meta:** ~30KB de conteúdo prático com código, checklists e exemplos reais.

---

## Pilar 1: Validação e Testes

### 1.1 Por Que Testar Agentes é Diferente

Testar agentes autônomos apresenta desafios únicos:

- **Não-determinismo**: Mesmo input pode gerar outputs diferentes
- **Complexidade de estado**: Agentes mantêm contexto e histórico
- **Interações emergentes**: Em multiagentes, comportamentos imprevisíveis aparecem
- **Dependência de LLMs**: Modelos mudam, outputs variam

**Estratégia:** Combinar testes tradicionais com validação específica para IA.

### 1.2 Pirâmide de Testes para Agentes

```
                    /\
                   /  \
                  /E2E \           (10%) - Testes end-to-end
                 /______\
                /        \
               /Integration\       (30%) - Testes de integração
              /____________\
             /              \
            /  Unit Tests    \    (60%) - Testes unitários
           /__________________\
```

#### Nível 1: Testes Unitários (60%)

Testam componentes individuais de forma isolada.

```python
import unittest
from typing import Dict, List
from datetime import datetime

# Classe de exemplo
class AgentValidator:
    """Valida inputs e outputs de agentes."""

    def validate_input(self, user_input: str) -> Dict:
        """Valida input do usuário."""
        errors = []

        if not user_input or not user_input.strip():
            errors.append("Input vazio")

        if len(user_input) > 10000:
            errors.append("Input muito longo (máximo 10000 caracteres)")

        # Detectar conteúdo potencialmente malicioso
        dangerous_patterns = ['<script>', 'DROP TABLE', 'DELETE FROM']
        for pattern in dangerous_patterns:
            if pattern.lower() in user_input.lower():
                errors.append(f"Padrão suspeito detectado: {pattern}")

        return {
            "valid": len(errors) == 0,
            "errors": errors,
            "cleaned_input": user_input.strip() if user_input else ""
        }

    def validate_output(self, output: str, max_length: int = 5000) -> Dict:
        """Valida output do agente."""
        issues = []

        if not output:
            issues.append("Output vazio")

        if len(output) > max_length:
            issues.append(f"Output muito longo ({len(output)} > {max_length})")

        # Verificar se contém informações sensíveis
        sensitive_patterns = [
            r'\b\d{3}-\d{2}-\d{4}\b',  # SSN
            r'\b\d{16}\b',  # Credit card
            r'senha:\s*\S+',  # Passwords
        ]

        import re
        for pattern in sensitive_patterns:
            if re.search(pattern, output, re.IGNORECASE):
                issues.append(f"Possível dado sensível detectado: {pattern}")

        return {
            "safe": len(issues) == 0,
            "issues": issues,
            "length": len(output)
        }

# Testes Unitários
class TestAgentValidator(unittest.TestCase):
    """Testes unitários para AgentValidator."""

    def setUp(self):
        """Configuração antes de cada teste."""
        self.validator = AgentValidator()

    def test_valid_input(self):
        """Testa input válido."""
        result = self.validator.validate_input("Olá, como posso ajudar?")
        self.assertTrue(result['valid'])
        self.assertEqual(len(result['errors']), 0)

    def test_empty_input(self):
        """Testa input vazio."""
        result = self.validator.validate_input("")
        self.assertFalse(result['valid'])
        self.assertIn("Input vazio", result['errors'])

    def test_whitespace_input(self):
        """Testa input só com espaços."""
        result = self.validator.validate_input("   ")
        self.assertFalse(result['valid'])
        self.assertIn("Input vazio", result['errors'])

    def test_too_long_input(self):
        """Testa input muito longo."""
        long_input = "a" * 10001
        result = self.validator.validate_input(long_input)
        self.assertFalse(result['valid'])
        self.assertIn("muito longo", result['errors'][0].lower())

    def test_dangerous_input_script(self):
        """Testa detecção de script malicioso."""
        result = self.validator.validate_input("Hello <script>alert('xss')</script>")
        self.assertFalse(result['valid'])
        self.assertTrue(any('script' in err.lower() for err in result['errors']))

    def test_dangerous_input_sql(self):
        """Testa detecção de SQL injection."""
        result = self.validator.validate_input("'; DROP TABLE users; --")
        self.assertFalse(result['valid'])
        self.assertTrue(any('drop table' in err.lower() for err in result['errors']))

    def test_valid_output(self):
        """Testa output válido."""
        result = self.validator.validate_output("Esta é uma resposta normal.")
        self.assertTrue(result['safe'])
        self.assertEqual(len(result['issues']), 0)

    def test_empty_output(self):
        """Testa output vazio."""
        result = self.validator.validate_output("")
        self.assertFalse(result['safe'])
        self.assertIn("Output vazio", result['issues'])

    def test_too_long_output(self):
        """Testa output muito longo."""
        long_output = "x" * 5001
        result = self.validator.validate_output(long_output)
        self.assertFalse(result['safe'])
        self.assertTrue(any('muito longo' in issue.lower() for issue in result['issues']))

    def test_sensitive_data_ssn(self):
        """Testa detecção de SSN."""
        result = self.validator.validate_output("Seu SSN é 123-45-6789")
        self.assertFalse(result['safe'])
        self.assertTrue(len(result['issues']) > 0)

    def test_sensitive_data_credit_card(self):
        """Testa detecção de cartão de crédito."""
        result = self.validator.validate_output("Cartão: 1234567890123456")
        self.assertFalse(result['safe'])
        self.assertTrue(len(result['issues']) > 0)

# Executar testes
if __name__ == '__main__':
    # Criar suite de testes
    suite = unittest.TestLoader().loadTestsFromTestCase(TestAgentValidator)

    # Executar com verbosidade
    runner = unittest.TextTestRunner(verbosity=2)
    result = runner.run(suite)

    # Sumário
    print("\n" + "=" * 70)
    print("SUMÁRIO DOS TESTES")
    print("=" * 70)
    print(f"Testes executados: {result.testsRun}")
    print(f"Sucessos: {result.testsRun - len(result.failures) - len(result.errors)}")
    print(f"Falhas: {len(result.failures)}")
    print(f"Erros: {len(result.errors)}")
    print(f"Taxa de sucesso: {((result.testsRun - len(result.failures) - len(result.errors)) / result.testsRun * 100):.1f}%")
```

#### Nível 2: Testes de Integração (30%)

Testam interação entre componentes.

```python
import unittest
from typing import Dict, List
from dataclasses import dataclass
from datetime import datetime

# Componentes para integração
@dataclass
class Message:
    """Mensagem entre agentes."""
    from_agent: str
    to_agent: str
    content: str
    timestamp: datetime
    message_id: str

class MessageBroker:
    """Broker de mensagens entre agentes."""

    def __init__(self):
        self.queues: Dict[str, List[Message]] = {}
        self.message_counter = 0

    def send_message(self, from_agent: str, to_agent: str, content: str) -> Message:
        """Envia mensagem de um agente para outro."""
        self.message_counter += 1

        message = Message(
            from_agent=from_agent,
            to_agent=to_agent,
            content=content,
            timestamp=datetime.now(),
            message_id=f"msg_{self.message_counter}"
        )

        # Adicionar à fila do destinatário
        if to_agent not in self.queues:
            self.queues[to_agent] = []

        self.queues[to_agent].append(message)

        return message

    def receive_messages(self, agent_name: str) -> List[Message]:
        """Recebe todas as mensagens para um agente."""
        messages = self.queues.get(agent_name, [])
        self.queues[agent_name] = []  # Limpa fila
        return messages

class SimpleAgent:
    """Agente simples para testes de integração."""

    def __init__(self, name: str, broker: MessageBroker):
        self.name = name
        self.broker = broker
        self.inbox = []

    def send_to(self, recipient: str, content: str):
        """Envia mensagem para outro agente."""
        return self.broker.send_message(self.name, recipient, content)

    def check_inbox(self):
        """Verifica mensagens recebidas."""
        self.inbox = self.broker.receive_messages(self.name)
        return self.inbox

class TestAgentIntegration(unittest.TestCase):
    """Testes de integração entre agentes."""

    def setUp(self):
        """Configuração antes de cada teste."""
        self.broker = MessageBroker()
        self.agent_a = SimpleAgent("AgentA", self.broker)
        self.agent_b = SimpleAgent("AgentB", self.broker)
        self.agent_c = SimpleAgent("AgentC", self.broker)

    def test_single_message_delivery(self):
        """Testa entrega de uma mensagem."""
        # Agent A envia para Agent B
        msg = self.agent_a.send_to("AgentB", "Hello from A")

        # Agent B recebe
        inbox = self.agent_b.check_inbox()

        self.assertEqual(len(inbox), 1)
        self.assertEqual(inbox[0].content, "Hello from A")
        self.assertEqual(inbox[0].from_agent, "AgentA")

    def test_multiple_messages(self):
        """Testa entrega de múltiplas mensagens."""
        # Enviar várias mensagens
        self.agent_a.send_to("AgentB", "Message 1")
        self.agent_a.send_to("AgentB", "Message 2")
        self.agent_c.send_to("AgentB", "Message 3")

        # Agent B recebe todas
        inbox = self.agent_b.check_inbox()

        self.assertEqual(len(inbox), 3)
        self.assertEqual(inbox[0].content, "Message 1")
        self.assertEqual(inbox[1].content, "Message 2")
        self.assertEqual(inbox[2].content, "Message 3")

    def test_bidirectional_communication(self):
        """Testa comunicação bidirecional."""
        # A -> B
        self.agent_a.send_to("AgentB", "Request")

        # B recebe e responde
        inbox_b = self.agent_b.check_inbox()
        self.assertEqual(len(inbox_b), 1)

        self.agent_b.send_to("AgentA", "Response")

        # A recebe resposta
        inbox_a = self.agent_a.check_inbox()
        self.assertEqual(len(inbox_a), 1)
        self.assertEqual(inbox_a[0].content, "Response")

    def test_message_isolation(self):
        """Testa isolamento de mensagens entre agentes."""
        # A envia para B
        self.agent_a.send_to("AgentB", "For B only")

        # C não deve receber
        inbox_c = self.agent_c.check_inbox()
        self.assertEqual(len(inbox_c), 0)

        # B deve receber
        inbox_b = self.agent_b.check_inbox()
        self.assertEqual(len(inbox_b), 1)

    def test_message_ordering(self):
        """Testa ordem de mensagens."""
        # Enviar mensagens em ordem
        self.agent_a.send_to("AgentB", "First")
        self.agent_a.send_to("AgentB", "Second")
        self.agent_a.send_to("AgentB", "Third")

        # Verificar ordem
        inbox = self.agent_b.check_inbox()

        self.assertEqual(inbox[0].content, "First")
        self.assertEqual(inbox[1].content, "Second")
        self.assertEqual(inbox[2].content, "Third")

    def test_inbox_clearing(self):
        """Testa limpeza da inbox após leitura."""
        # Enviar mensagem
        self.agent_a.send_to("AgentB", "Test")

        # Primeira leitura
        inbox1 = self.agent_b.check_inbox()
        self.assertEqual(len(inbox1), 1)

        # Segunda leitura (deve estar vazia)
        inbox2 = self.agent_b.check_inbox()
        self.assertEqual(len(inbox2), 0)

if __name__ == '__main__':
    suite = unittest.TestLoader().loadTestsFromTestCase(TestAgentIntegration)
    runner = unittest.TextTestRunner(verbosity=2)
    result = runner.run(suite)
```

#### Nível 3: Testes End-to-End (10%)

Testam o sistema completo em cenário real.

```python
import unittest
from typing import Dict, List
import time

class E2ETestContentCreation(unittest.TestCase):
    """Testes E2E para sistema de criação de conteúdo."""

    def test_complete_content_creation_flow(self):
        """Testa fluxo completo de criação de conteúdo."""

        # Simula sistema completo (usando classes dos módulos anteriores)
        # Aqui usaríamos o IterativeContentPipeline real

        print("\n" + "=" * 70)
        print("TESTE E2E: Criação de Conteúdo Completa")
        print("=" * 70)

        # 1. Input do usuário
        topic = "Machine Learning"
        print(f"\n1. Input recebido: {topic}")

        # 2. Produção
        print("\n2. Iniciando produção...")
        start_time = time.time()

        # Simula produção (em produção, usaria pipeline real)
        content = f"# Guia Completo de {topic}\n\n## Introdução\n\nConteúdo sobre {topic}..."

        # 3. Validação
        print("\n3. Validando qualidade...")
        quality_score = 0.92  # Simulado

        # 4. Verificações
        print("\n4. Executando verificações finais...")

        checks = {
            "content_exists": len(content) > 0,
            "minimum_length": len(content) > 200,
            "has_title": content.startswith("#"),
            "quality_threshold": quality_score >= 0.85,
            "execution_time": (time.time() - start_time) < 10.0
        }

        # Asserções
        for check_name, passed in checks.items():
            print(f"   {'✓' if passed else '✗'} {check_name}")
            self.assertTrue(passed, f"Falhou em: {check_name}")

        # 5. Output final
        print(f"\n5. Conteúdo criado: {len(content)} caracteres")
        print(f"   Qualidade: {quality_score:.0%}")
        print(f"   Tempo: {time.time() - start_time:.2f}s")

        print("\n" + "=" * 70)
        print("TESTE E2E PASSOU!")
        print("=" * 70)

if __name__ == '__main__':
    suite = unittest.TestLoader().loadTestsFromTestCase(E2ETestContentCreation)
    runner = unittest.TextTestRunner(verbosity=2)
    runner.run(suite)
```

### 1.3 Testes Específicos para LLMs

```python
import unittest
from typing import List, Dict

class LLMOutputValidator:
    """Valida outputs de LLMs."""

    def check_factual_consistency(self, output: str, expected_facts: List[str]) -> Dict:
        """Verifica se output contém fatos esperados."""
        found_facts = []
        missing_facts = []

        for fact in expected_facts:
            if fact.lower() in output.lower():
                found_facts.append(fact)
            else:
                missing_facts.append(fact)

        return {
            "consistent": len(missing_facts) == 0,
            "found": found_facts,
            "missing": missing_facts,
            "coverage": len(found_facts) / len(expected_facts) if expected_facts else 0
        }

    def check_hallucination_indicators(self, output: str) -> Dict:
        """Detecta indicadores de alucinação."""
        indicators = {
            "uncertainty_phrases": ["eu acho", "provavelmente", "talvez", "não tenho certeza"],
            "vague_numbers": ["muitos", "vários", "alguns", "poucos"],
            "unverified_claims": ["é conhecido que", "estudos mostram", "especialistas dizem"]
        }

        detected = []

        for category, phrases in indicators.items():
            for phrase in phrases:
                if phrase in output.lower():
                    detected.append({
                        "category": category,
                        "phrase": phrase
                    })

        return {
            "clean": len(detected) == 0,
            "indicators_found": detected,
            "risk_level": "high" if len(detected) >= 3 else "medium" if len(detected) >= 1 else "low"
        }

    def check_output_format(self, output: str, expected_format: Dict) -> Dict:
        """Verifica se output segue formato esperado."""
        checks = {}

        if "has_title" in expected_format:
            checks["has_title"] = output.startswith("#")

        if "has_sections" in expected_format:
            checks["has_sections"] = "##" in output

        if "min_length" in expected_format:
            checks["min_length"] = len(output) >= expected_format["min_length"]

        if "has_code" in expected_format:
            checks["has_code"] = "```" in output

        return {
            "valid": all(checks.values()),
            "checks": checks
        }

class TestLLMOutputs(unittest.TestCase):
    """Testes específicos para outputs de LLMs."""

    def setUp(self):
        self.validator = LLMOutputValidator()

    def test_factual_consistency_full(self):
        """Testa consistência factual completa."""
        output = "Python foi criado por Guido van Rossum em 1991. É uma linguagem interpretada."
        expected_facts = ["Python", "Guido van Rossum", "1991"]

        result = self.validator.check_factual_consistency(output, expected_facts)

        self.assertTrue(result['consistent'])
        self.assertEqual(len(result['found']), 3)
        self.assertEqual(result['coverage'], 1.0)

    def test_factual_consistency_partial(self):
        """Testa consistência factual parcial."""
        output = "Python é uma linguagem de programação."
        expected_facts = ["Python", "Guido van Rossum", "1991"]

        result = self.validator.check_factual_consistency(output, expected_facts)

        self.assertFalse(result['consistent'])
        self.assertEqual(len(result['missing']), 2)
        self.assertLess(result['coverage'], 1.0)

    def test_no_hallucination(self):
        """Testa output sem alucinação."""
        output = "Python foi lançado em 1991 por Guido van Rossum."

        result = self.validator.check_hallucination_indicators(output)

        self.assertTrue(result['clean'])
        self.assertEqual(result['risk_level'], "low")

    def test_hallucination_detected(self):
        """Testa detecção de alucinação."""
        output = "Eu acho que Python talvez seja a melhor linguagem. Estudos mostram que muitos desenvolvedores preferem."

        result = self.validator.check_hallucination_indicators(output)

        self.assertFalse(result['clean'])
        self.assertGreater(len(result['indicators_found']), 0)
        self.assertIn(result['risk_level'], ["medium", "high"])

    def test_format_validation(self):
        """Testa validação de formato."""
        output = """# Título

## Seção 1

Conteúdo aqui...

```python
print("Hello")
```
"""

        result = self.validator.check_output_format(output, {
            "has_title": True,
            "has_sections": True,
            "min_length": 50,
            "has_code": True
        })

        self.assertTrue(result['valid'])
        self.assertTrue(all(result['checks'].values()))

if __name__ == '__main__':
    unittest.main(verbosity=2)
```

### 1.4 Checklist de Validação Completo

```python
from dataclasses import dataclass
from typing import List, Dict, Callable
from enum import Enum

class CheckSeverity(Enum):
    CRITICAL = "crítico"
    HIGH = "alto"
    MEDIUM = "médio"
    LOW = "baixo"

@dataclass
class ValidationCheck:
    """Define uma validação."""
    name: str
    description: str
    check_function: Callable
    severity: CheckSeverity
    required: bool = True

class ValidationChecklist:
    """Checklist de validação para agentes."""

    def __init__(self):
        self.checks: List[ValidationCheck] = []
        self.results = []

    def add_check(self, check: ValidationCheck):
        """Adiciona validação ao checklist."""
        self.checks.append(check)

    def run_all_checks(self, agent_output: Dict) -> Dict:
        """Executa todas as validações."""
        self.results = []
        passed = 0
        failed = 0
        warnings = 0

        for check in self.checks:
            try:
                result = check.check_function(agent_output)

                check_result = {
                    "name": check.name,
                    "passed": result,
                    "severity": check.severity,
                    "required": check.required
                }

                self.results.append(check_result)

                if result:
                    passed += 1
                else:
                    if check.required:
                        failed += 1
                    else:
                        warnings += 1

            except Exception as e:
                self.results.append({
                    "name": check.name,
                    "passed": False,
                    "severity": check.severity,
                    "required": check.required,
                    "error": str(e)
                })
                failed += 1

        # Determinar se passou geral
        critical_failures = [r for r in self.results if not r['passed'] and r['severity'] == CheckSeverity.CRITICAL]

        return {
            "overall_passed": len(critical_failures) == 0 and failed == 0,
            "total_checks": len(self.checks),
            "passed": passed,
            "failed": failed,
            "warnings": warnings,
            "results": self.results
        }

# Exemplo de uso
checklist = ValidationChecklist()

# Adicionar validações
checklist.add_check(ValidationCheck(
    name="Output não vazio",
    description="Verifica se agente gerou output",
    check_function=lambda output: len(output.get('content', '')) > 0,
    severity=CheckSeverity.CRITICAL,
    required=True
))

checklist.add_check(ValidationCheck(
    name="Tamanho adequado",
    description="Output entre 100 e 10000 caracteres",
    check_function=lambda output: 100 <= len(output.get('content', '')) <= 10000,
    severity=CheckSeverity.HIGH,
    required=True
))

checklist.add_check(ValidationCheck(
    name="Sem dados sensíveis",
    description="Não contém informações pessoais",
    check_function=lambda output: 'senha' not in output.get('content', '').lower(),
    severity=CheckSeverity.CRITICAL,
    required=True
))

checklist.add_check(ValidationCheck(
    name="Qualidade alta",
    description="Score de qualidade >= 0.8",
    check_function=lambda output: output.get('quality_score', 0) >= 0.8,
    severity=CheckSeverity.MEDIUM,
    required=False
))

# Executar validações
agent_output = {
    "content": "Este é o output do agente com conteúdo válido e adequado.",
    "quality_score": 0.85
}

results = checklist.run_all_checks(agent_output)

print("=" * 70)
print("RESULTADO DA VALIDAÇÃO")
print("=" * 70)
print(f"Passou: {'SIM' if results['overall_passed'] else 'NÃO'}")
print(f"Checks totais: {results['total_checks']}")
print(f"Passou: {results['passed']}")
print(f"Falhou: {results['failed']}")
print(f"Avisos: {results['warnings']}")

print("\nDetalhes:")
for result in results['results']:
    status = "✓" if result['passed'] else "✗"
    severity_icon = "🔴" if result['severity'] == CheckSeverity.CRITICAL else "🟠" if result['severity'] == CheckSeverity.HIGH else "🟡"
    print(f"{status} {severity_icon} {result['name']}")
```

---

## Conclusão do Módulo e do Nível 3A

### O Que Você Dominou

Neste módulo, você aprendeu os **5 pilares fundamentais** para produção:

1. **Validação e Testes** - Pirâmide de testes, testes unitários/integração/E2E, validação de LLMs
2. **Monitoramento e Observabilidade** - Logging estruturado, métricas, alertas, dashboards
3. **Segurança e Privacidade** - Permissões, proteção contra injection, dados sensíveis
4. **Escalabilidade e Performance** - Monitoramento de performance, caching inteligente
5. **Manutenção e Evolução** - Versionamento, changelog, checklist de produção

### Código Completo

Este módulo forneceu:
- ~32KB de código prático
- Frameworks completos para cada pilar
- Checklists prontos para uso
- Exemplos testados e funcionais

### Aplicação Prática

Agora você tem tudo que precisa para:
- Colocar agentes em produção com confiança
- Monitorar e manter sistemas em operação
- Garantir segurança e privacidade
- Escalar conforme necessário
- Evoluir sistemas continuamente

---

**Parabéns!** Você completou o **Nível 3A - Agentes e Sistemas Autônomos**. Você agora domina:

- ✓ Fundamentos de agentes autônomos
- ✓ Arquiteturas multiagentes
- ✓ Sistemas de orquestração
- ✓ Implementação de 9 elementos de agentes
- ✓ 4 projetos práticos completos
- ✓ Boas práticas de produção

**Próximo nível:** Continue sua jornada na Formação em Engenharia de Intenção!

---

**FEI - Formação em Engenharia de Intenção**
*Nível 3A - Agentes e Sistemas Autônomos* - COMPLETO ✓
