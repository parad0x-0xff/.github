# 🔒 Security Scan Configuration

Este projeto inclui uma configuração simplificada de segurança usando Semgrep para detectar vulnerabilidades no código.

## 📋 Arquivos de Configuração

### 1. `semgrep-scan.yml`
Action reutilizável do GitHub Actions que executa o scan de segurança com Semgrep.

**Características principais:**
- ✅ **Não trava a pipeline**: Timeout configurado para 30 minutos
- ✅ **Salva no GitHub Code Scanning**: Resultados são enviados automaticamente
- ✅ **Configurável**: Múltiplos parâmetros de entrada
- ✅ **Relatórios detalhados**: Gera relatórios com resumo das vulnerabilidades
- ✅ **Falha opcional**: Pode ser configurado para falhar apenas em vulnerabilidades críticas
- ✅ **Sem duplicatas**: Configuração de concurrency evita execuções simultâneas

### 2. `.semgrep.yml`
Configuração simplificada do Semgrep que usa apenas regras da comunidade.

**Características:**
- 🎯 **Regras da comunidade**: Usa apenas regras oficiais do Semgrep
- 🔍 **Detecção automática**: Semgrep detecta automaticamente todas as linguagens
- 🚫 **Exclusões inteligentes**: Remove falsos positivos comuns
- ⚡ **Performance otimizada**: Configurações de memória e timeout
- 🔍 **Filtros de severidade**: Apenas ERROR e WARNING por padrão

## 🚀 Como Usar

### Uso Básico
```yaml
jobs:
  security-scan:
    uses: ./.github/workflows/semgrep-scan.yml
    with:
      semgrep-rules: '.semgrep.yml'
      fail-on-findings: false
```

### Uso Avançado
```yaml
jobs:
  security-scan:
    uses: ./.github/workflows/semgrep-scan.yml
    with:
      semgrep-rules: '.semgrep.yml'
      fail-on-findings: true
      exclude-patterns: 'node_modules/**,dist/**,build/**,*.min.js,*.bundle.js'
      severity-filter: 'ERROR'
```

## 📊 Parâmetros de Entrada

| Parâmetro | Tipo | Padrão | Descrição |
|-----------|------|--------|-----------|
| `semgrep-rules` | string | `'p/ci'` | Conjunto de regras Semgrep a ser usado |
| `fail-on-findings` | boolean | `false\|true` | Falhar se encontrar vulnerabilidades críticas |
| `exclude-patterns` | string | `'node_modules/**,dist/**,build/**,*.min.js,*.bundle.js'` | Padrões de exclusão |
| `severity-filter` | string | `'ERROR,WARNING,INFO'` | Filtro de severidade |

## 🔍 Tipos de Vulnerabilidades Detectadas

### **Detecção Automática por Linguagem**

O Semgrep detecta automaticamente as linguagens presentes no repositório e aplica as regras relevantes:


## 📈 Resultados e Relatórios

### GitHub Code Scanning
- ✅ Resultados são automaticamente enviados para o GitHub Security
- ✅ Integração com GitHub Security tab
- ✅ Histórico de vulnerabilidades mantido
- ✅ Categoria única para evitar duplicatas

### Relatórios Detalhados
- 📊 Resumo por severidade (Critical/High, Medium, Low)
- 🚨 Top 5 vulnerabilidades mais críticas
- 📝 Mensagens de sucesso/erro claras

## ⚙️ Configurações de Performance

- **Timeout**: 30 minutos (evita travamentos)
- **Memória**: 4GB máximo
- **Fetch Depth**: 0 (análise completa do histórico)
- **Auto-config**: Ativado para reduzir falsos positivos
- **Concurrency**: Evita execuções simultâneas

## 🛡️ Boas Práticas

1. **Use `fail-on-findings: false`** em PRs para não bloquear o desenvolvimento
2. **Use `fail-on-findings: true`** apenas para branches principais
3. **Configure exclusões específicas** para seu projeto
4. **Monitore regularmente** os resultados no GitHub Security tab
5. **Mantenha o arquivo `.semgrep.yml`** na raiz do repositório

## 🔧 Troubleshooting

### Pipeline travando
- Verifique se o timeout está adequado (30 min padrão)
- Ajuste as exclusões para reduzir arquivos analisados
- Use `severity-filter: 'ERROR'` para reduzir processamento

### Muitos falsos positivos
- Adicione mais padrões em `exclude-patterns`
- Ajuste o `severity-filter` para ser mais restritivo
- Revise as exclusões no `.semgrep.yml`

### Resultados não aparecem no Code Scanning
- Verifique se as permissões estão corretas
- Confirme se o arquivo SARIF está sendo gerado
- Verifique se o step de upload está executando

### Erro "Unexpected value 'rules'"
- Certifique-se de que `.semgrep.yml` está na raiz do repositório
- Não coloque o arquivo dentro de `.github/workflows/`

## 🌐 Reutilização em Outros Repositórios

Para usar este workflow em outros repositórios:

```yaml
jobs:
  semgrep-security:
    uses: <owner>/<repo>/.github/workflows/semgrep-scan.yml@<branch>
    with:
      semgrep-rules: '.semgrep.yml'
      fail-on-findings: ${{ github.event_name == 'pull_request' }}
      exclude-patterns: 'node_modules/**,dist/**,build/**,*.min.js,*.bundle.js'
      severity-filter: ${{ github.event_name == 'pull_request' && 'ERROR' || 'ERROR,WARNING' }}
    secrets: inherit
```

**Exemplo:**
```yaml
uses: parad0x-0xff/.github/workflows/semgrep-scan.yml@main
```

## 📚 Recursos Adicionais

- [Semgrep Documentation](https://semgrep.dev/docs/)
- [GitHub Code Scanning](https://docs.github.com/en/code-security/code-scanning)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [GitHub Reusable Workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) 
