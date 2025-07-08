# 🛡️ Análise de Segurança com Semgrep (Workflow Reutilizável)

Este repositório contém um workflow reutilizável do GitHub Actions para executar análises de segurança estática de código (SAST) com [Semgrep](https://semgrep.dev/). Ele foi projetado para ser facilmente integrado em qualquer pipeline de CI/CD, ajudando a encontrar vulnerabilidades antes que cheguem em produção.

## ✨ Principais Vantagens

- **✅ Workflow Reutilizável**: Integre a análise de segurança em qualquer repositório com poucas linhas de código.
- **📊 Integração com Code Scanning**: Envia os resultados diretamente para a aba **Security** do GitHub, permitindo um gerenciamento centralizado das vulnerabilidades.
- **💬 Comentários em Pull Requests**: Posta um resumo dos alertas críticos (`ERROR`) diretamente no PR para feedback rápido.
- **⚙️ Altamente Configurável**: Personalize as regras, a severidade dos alertas e os arquivos a serem ignorados.
- **🚦 Controle de Pipeline**: Configure o workflow para falhar o build apenas em branches principais ou em qualquer detecção, dando flexibilidade ao seu processo.
- **⚡ Performance Otimizada**: Configurado com timeout de 30 minutos e exclusões inteligentes para evitar scans desnecessários e lentidão.

## 🚀 Como Usar

Para usar, adicione o security-scan.yml no repositório que deseja escanear.
Crie um repositório especial no github chamado `.github`. Desse modo é possível que você utilize a técnica de workflows reutilizáveis.

No repositório especial, adicione o `semgrep-scan.yml` para ter a gestão total do scan de todos os repositórios. 
Ganhando escalabilidade e controle na hora de mudar um regra para um repositório, essa alteração é replicada para todos os repositórios automaticamente.

## ⚙️ Configuração

### Entradas do Workflow (Inputs)

| Parâmetro | Tipo | Padrão | Descrição |
|-----------|------|--------|-----------|
| `semgrep-rules` | string | `'p/ci'` | Conjunto de regras Semgrep a ser usado |
| `fail-on-findings` | boolean | `false\|true` | Falhar se encontrar vulnerabilidades críticas |
| `exclude-patterns` | string | `'node_modules/**,dist/**,build/**,*.min.js,*.bundle.js'` | Padrões de exclusão |
| `severity-filter` | string | `'ERROR,WARNING,INFO'` | Filtro de severidade |
| `semgrep-rules` | `string` | `p/ci` | Conjunto de regras do Semgrep a ser usado. Pode ser um arquivo local (ex: `.semgrep.yml`) ou um do registry (ex: `p/ci`). |
| `fail-on-findings` | `boolean` | `false` | Se `true`, o workflow falhará se qualquer vulnerabilidade for encontrada (respeitando o `severity-filter`). |
| `exclude-patterns` | `string` | `''` | Padrões de arquivos ou diretórios a serem ignorados durante o scan, separados por espaço (ex: `'tests/ node_modules/'`). |
| `severity-filter` | `string` | `ERROR,WARNING,INFO` | Filtra os resultados por severidade. Opções: `ERROR`, `WARNING`, `INFO`. |

## 🔍 Tipos de Vulnerabilidades Detectadas

### Segredos (Secrets)

| Segredo | Obrigatório | Descrição |
|-----------|------|--------|
| `github-token` | Sim | Token do GitHub (`secrets.GITHUB_TOKEN`) com permissões de `write` para `security-events` e `pull-requests` para enviar os resultados. |
| `semgrep-token` | Não | Token do Semgrep (`secrets.SEMGREP_APP_TOKEN`) para realizar o scan do semgrep com acesso a mais regras. |

### **Detecção Automática por Linguagem**

O Semgrep detecta automaticamente as linguagens presentes no repositório e aplica as regras relevantes:

## 📈 Resultados e Relatórios

### GitHub Code Scanning

- ✅ Resultados são automaticamente enviados para o GitHub Security
- ✅ Integração com GitHub Security tab
- ✅ Histórico de vulnerabilidades mantido
- ✅ Categoria única para evitar duplicatas

### Comentários no Pull Request

Para cada Pull Request, o workflow analisará as mudanças e, se encontrar vulnerabilidades críticas (`ERROR`), postará um comentário detalhado com a lista de problemas, facilitando a correção antes do merge.

### Relatórios Detalhados (WIP)

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

Todos os resultados, independentemente da severidade, são processados e enviados para a aba **Security > Code scanning alerts** do GitHub. Isso cria um histórico de segurança e permite gerenciar o ciclo de vida de cada vulnerabilidade.

## 🔧 Troubleshooting

### Pipeline travando

- Verifique se o timeout está adequado (30 min padrão)
- Ajuste as exclusões para reduzir arquivos analisados
- Use `severity-filter: 'ERROR'` para reduzir processamento

### Workflow falha com erro de permissão

- Vá em **Settings > Actions > General** no seu repositório.
- Em **Workflow permissions**, certifique-se de que a opção **Read and write permissions** está marcada. Isso é necessário para que a Action possa escrever no Code Scanning e comentar em PRs.

### Muitos falsos positivos

- Adicione mais padrões em `exclude-patterns`
- Ajuste o `severity-filter` para ser mais restritivo
- Revise as exclusões no `.semgrep-scan.yml`

### Muitos falsos positivos nos resultados

- Use o parâmetro `exclude-patterns` para ignorar diretórios de teste, dependências ou arquivos gerados.
- Refine as regras no seu arquivo `.semgrep-scan.yml` ou escolha um conjunto de regras mais restrito, como `p/security-audit`.

### Resultados não aparecem no Code Scanning

- Verifique se as permissões estão corretas
- Confirme se o arquivo SARIF está sendo gerado
- Verifique se o step de upload está executando

### Erro "Unexpected value 'rules'

- Certifique-se de que `.semgrep-scan.yml` está na raiz do repositório
- Não coloque o arquivo dentro de `.github/workflows/`

### O scan está muito lento

- O tempo de execução depende do tamanho do código. Use `exclude-patterns` para remover arquivos desnecessários e acelerar a análise.
- Considere usar um `severity-filter` mais estrito (`ERROR`) em Pull Requests para uma verificação mais rápida.

## 📚 Recursos Adicionais

- [Semgrep Documentation](https://semgrep.dev/docs/getting-started/quickstart)
- [GitHub Code Scanning](https://docs.github.com/en/code-security/code-scanning/introduction-to-code-scanning/about-code-scanning)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [GitHub Reusable Workflows](https://docs.github.com/en/actions/how-tos/sharing-automations/reusing-workflows)
