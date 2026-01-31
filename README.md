# VAmPI – DevSecOps Security Pipeline (SAST + DAST)

Este repositório utiliza o projeto **VAmPI (Vulnerable API)** como aplicação alvo para demonstrar a implementação de uma **pipeline de segurança DevSecOps**, integrando ferramentas de **SAST** e **DAST** em um workflow automatizado com **GitHub Actions**.

O objetivo deste projeto não é corrigir as vulnerabilidades da aplicação, mas sim **demonstrar como testes de segurança podem ser integrados ao ciclo de desenvolvimento** de forma automatizada, reproduzível e visível no CI.

---

## Objetivo do Case

Atender aos seguintes requisitos:

- Clonar o repositório VAmPI
- Implementar uma pipeline contendo:
  - **SAST (Static Application Security Testing)**
  - **DAST (Dynamic Application Security Testing)**
- Explicar a finalidade e o processo de implementação das ferramentas escolhidas
- Disponibilizar o projeto em um repositório público com os testes funcionando

---

## Visão Geral da Pipeline

A pipeline de segurança foi implementada utilizando **GitHub Actions** e é executada automaticamente em eventos de:

- `push` na branch principal
- `pull_request`

Ela é composta por dois estágios principais:

1. **SAST – Análise Estática de Código**
2. **DAST – Análise Dinâmica da Aplicação em Execução**

O workflow completo está definido em:

- `.github/workflows/security.yml`

---

## SAST – Static Application Security Testing

### Ferramenta escolhida: **Semgrep**

O **Semgrep** foi escolhido por ser uma ferramenta moderna de análise estática, com fácil integração em pipelines de CI/CD e bom equilíbrio entre cobertura e taxa de falsos positivos.

### Como funciona na pipeline

- O código-fonte do repositório é analisado sem necessidade de build ou execução da aplicação.
- É utilizado o conjunto de regras `p/ci`, voltado para práticas inseguras comuns em aplicações.
- Os resultados são gerados no formato **SARIF** e enviados para o **GitHub Code Scanning**, ficando visíveis na aba **Security** do repositório.

### Benefícios do SAST

- Detecção precoce de padrões inseguros
- Análise rápida e automatizada
- Feedback contínuo para desenvolvedores durante o ciclo de desenvolvimento

---

## DAST – Dynamic Application Security Testing

### Ferramenta escolhida: **OWASP ZAP (Baseline Scan)**

O **OWASP ZAP** foi utilizado para realizar análise dinâmica da aplicação, simulando como um atacante externo poderia observar falhas de segurança via HTTP.

Foi escolhido o modo **Baseline**, que executa principalmente **análises passivas**, sem explorar ativamente a aplicação.

### Como funciona na pipeline

1. A aplicação VAmPI é iniciada automaticamente via **Docker Compose** no runner do GitHub Actions.
2. A instância **vulnerable** do VAmPI é exposta localmente na porta `5002`.
3. A pipeline aguarda até que a API esteja acessível.
4. O endpoint `/createdb` é chamado para inicializar o banco de dados.
5. O ZAP executa o scan baseline contra `http://localhost:5002`.
6. Relatórios são gerados e armazenados como **artifacts** do workflow.

### Relatórios gerados

- `report_html.html`
- `report_json.json`
- `report_md.md`

Esses arquivos ficam disponíveis para download ao final da execução do workflow.

---

## Observações Importantes sobre o DAST

O VAmPI é uma **API**, e não uma aplicação web tradicional. Por esse motivo:

- Não há navegação baseada em links a partir do endpoint raiz (`/`)
- Muitos endpoints vulneráveis exigem autenticação ou parâmetros específicos
- O **ZAP Baseline depende de descoberta de URLs**

Por isso, o scan baseline tende a identificar principalmente:
- Problemas de headers HTTP
- Configurações inseguras
- Possíveis vazamentos de informação

A detecção de vulnerabilidades mais profundas (como SQL Injection, BOLA, falhas de autenticação) exigiria:
- Scan ativo (ZAP Full Scan)
- Autenticação automatizada
- Seeding explícito de endpoints da API

Essa limitação é esperada e condizente com o tipo de scan escolhido.

---

## Por que essa abordagem?

A escolha por **SAST + DAST Baseline** reflete um cenário comum em pipelines reais:

- **SAST** fornece feedback rápido e antecipado
- **DAST Baseline** adiciona uma camada de validação dinâmica sem alto risco ou impacto
- A pipeline é estável, reprodutível e adequada para execução automática em CI

Essa combinação permite evoluções futuras, como:
- Thresholds de severidade
- Scan ativo em ambientes controlados
- Autenticação automatizada no DAST
- Integração com sistemas de gestão de vulnerabilidades

---

## Conclusão

Este projeto demonstra como práticas de **DevSecOps** podem ser aplicadas para integrar segurança ao ciclo de desenvolvimento de software, utilizando ferramentas amplamente adotadas e workflows automatizados.

A pipeline implementada atende aos requisitos do case e fornece uma base sólida para evolução contínua da postura de segurança da aplicação.

---

## Referências

- VAmPI – Vulnerable API  
  https://github.com/erev0s/VAmPI

- Semgrep  
  https://semgrep.dev/

- OWASP ZAP  
  https://www.zaproxy.org/

