# VAmPI – DevSecOps Security Pipeline (SAST + DAST)

Este repositório demonstra a implementação de uma **pipeline DevSecOps** utilizando o projeto **VAmPI (Vulnerable API)** como aplicação alvo, integrando ferramentas de **SAST** e **DAST** em um workflow automatizado com **GitHub Actions**.

---

## Evidências da Pipeline em Execução

- **Workflow executando com sucesso (GitHub Actions):**  
  https://github.com/martalais/vampi-devsecops-pipeline/actions/runs/21537433197

- **Artefatos gerados pelo DAST (OWASP ZAP):**  
  https://github.com/martalais/vampi-devsecops-pipeline/actions/runs/21537433197/artifacts/5326634960

- **Finding do SAST visível em Code Scanning:**  
  https://github.com/martalais/vampi-devsecops-pipeline/security/code-scanning

---

## Objetivo do Case

Atender aos requisitos propostos no desafio técnico de DevSecOps:

- Clonar o repositório VAmPI
- Criar um workflow com:
  - **SAST (Static Application Security Testing)**
  - **DAST (Dynamic Application Security Testing)**
- Explicar a finalidade e a implementação das ferramentas de segurança
- Disponibilizar o projeto em um repositório público com os testes funcionando

---

## Visão Geral da Pipeline

A pipeline é executada automaticamente em eventos de `push` e `pull_request` e está definida em: `.github/workflows/security.yml`


Ela é composta por dois estágios principais:

1. **SAST – Análise estática do código**
2. **DAST – Análise dinâmica da aplicação em execução**

---

## SAST – Static Application Security Testing

### Ferramenta: **Semgrep**

O **Semgrep** foi utilizado para análise estática do código-fonte, identificando padrões inseguros sem a necessidade de executar a aplicação.

### Configuração adotada

- Foi utilizado o ruleset **`p/security-audit`**, que oferece uma análise mais abrangente do que o conjunto padrão de CI.
- Os resultados são exportados em formato **SARIF** e enviados para o **GitHub Code Scanning**, permitindo visualização direta na aba **Security** do repositório.

### Por que `p/security-audit`?

O ruleset `p/security-audit` aumenta a cobertura da análise estática, sendo mais adequado para fins de auditoria e demonstração de achados de segurança em um contexto de avaliação técnica.

---

## DAST – Dynamic Application Security Testing

### Ferramenta: **OWASP ZAP (Baseline Scan)**

O **OWASP ZAP** foi utilizado para realizar análise dinâmica da aplicação, executando um **scan passivo (baseline)** contra a API em execução.

### Funcionamento na pipeline

1. A aplicação VAmPI é iniciada automaticamente via **Docker Compose** no runner do GitHub Actions.
2. A instância vulnerável é exposta localmente na porta `5002`.
3. O banco de dados é inicializado via endpoint `/createdb`.
4. O ZAP executa o scan baseline contra `http://localhost:5002`.
5. Relatórios são gerados e disponibilizados como artifacts do workflow.

### Relatórios gerados

- `report_html.html`
- `report_json.json`
- `report_md.md`

---

## Observações sobre o DAST

O VAmPI é uma **API**, não uma aplicação web tradicional. Como o scan utilizado é **passivo** e depende de descoberta automática de URLs:

- O ZAP identifica principalmente:
  - headers inseguros
  - problemas de configuração
  - potenciais vazamentos de informação
- Vulnerabilidades mais profundas (ex.: SQL Injection, BOLA, falhas de autenticação) exigiriam:
  - scan ativo
  - autenticação automatizada
  - seeding explícito de endpoints da API

Essa limitação é esperada e condizente com o tipo de scan escolhido.

---

## Conclusão

Este projeto demonstra a integração prática de **SAST e DAST** em uma pipeline DevSecOps funcional, reproduzível e visível no CI, atendendo aos requisitos do desafio técnico e refletindo práticas comuns em ambientes reais de desenvolvimento seguro.

---

## Referências

- VAmPI – Vulnerable API  
  https://github.com/erev0s/VAmPI

- Semgrep  
  https://semgrep.dev/

- OWASP ZAP  
  https://www.zaproxy.org/




