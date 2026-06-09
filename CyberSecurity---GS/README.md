# Mission Clear — Motor Orbital para Missões LEO Seguras

> Projeto acadêmico — FIAP 3ESPR · Global Solution 2026
> Integração de Módulo de DevSecOps e Segurança Cibernética[cite: 5]

## 👥 Integrantes do Grupo
| Integrante | RM |
| :--- | :--- |
| Gustavo Bezerra Assumção | RM 553076 |
| Jó Sales | RM 552679 |
| Miguel Garcez de Carvalho | RM 553768 |
| Vinicius Souza e Silva | RM 552781 |
| Edson Leonardo | RM 553737 |

---

## 🌌 O Problema

Existem mais de **27.000 objetos rastreados** em órbita terrestre — detritos de satélites, estágios de foguetes e fragmentos de colisões. Uma missão LEO (Low Earth Orbit) mal planejada pode cruzar a trajetória de um detrito a 7,5 km/s. Sem uma análise prévia e automatizada de segurança, o risco de colisão permanece invisível, ameaçando infraestruturas críticas terrestres e espaciais.

**ODS Cobertos (Objetivos de Desenvolvimento Sustentável da ONU):**
* **9 · Indústria, Inovação e Infraestrutura:** Fortalecendo a resiliência das infraestruturas aeroespaciais e de comunicação.
* **11 · Cidades e Comunidades Sustentáveis:** Protegendo sistemas de geolocalização e monitoramento essenciais para a malha urbana.
* **13 · Ação Contra a Mudança Global do Clima:** Preservando satélites climáticos cruciais para a previsão de desastres na Terra.

---

## 🚀 A Solução

O **Mission Clear** ingere dados reais de detritos espaciais diretamente da API pública CelesTrak, propaga suas órbitas utilizando o modelo matemático SGP4 e calcula janelas de lançamento totalmente seguras para o destino escolhido. O operador consegue visualizar quais slots de tempo oferecem o menor risco de conjunção e simular a missão de forma dinâmica com alertas emitidos em tempo real via Server-Sent Events (SSE) durante o voo simulado.

> 📌 *Este repositório é exclusivamente voltado para a engenharia de **backend**: motor orbital + API REST + Módulo de Cibersegurança[cite: 5]. O aplicativo mobile (React Native / Expo) está hospedado no repositório `Fiap-3ESPR-GS-Mobile`.*

---

## 🛠️ Stack Técnico

* **Runtime:** .NET 10.0
* **Orquestrador:** .NET Aspire 9.1
* **API:** ASP.NET Core (Controllers)
* **Banco de Dados:** MySQL 8 · Pomelo EF Core · Migrations
* **Autenticação:** JWT Bearer (1h) + Refresh Token (7d)
* **Hash de Senhas:** BCrypt.Net-Next
* **Propagação Orbital:** SGP4 via NuGet
* **Testes Automatizados:** xUnit · FluentAssertions · Moq
* **Observabilidade:** OpenTelemetry · Aspire Dashboard

---

## 🔒 Módulo de DevSecOps e Segurança Cibernética

Em total conformidade com as diretrizes de Cibersegurança da FIAP[cite: 5], este repositório possui uma arquitetura de desenvolvimento seguro acoplada diretamente ao seu ciclo de vida (CI/CD)[cite: 5]. Abaixo estão descritos os controles práticos e as validações integradas[cite: 5]:

### 1. Arquivos da Implementação Prática (CI/CD Automatizado)[cite: 5]
O pipeline automatizado de validação está localizado no diretório `.github/workflows/security.yml`[cite: 5]. A esteira é disparada automaticamente a cada `push` ou `pull_request` nas branches principais, executando[cite: 5]:
* **Análise de Dependências (SCA):** Varredura ativa de vulnerabilidades conhecidas em bibliotecas open-source de terceiros (incluindo o motor NuGet SGP4 e o ecossistema OpenTelemetry) através do comando nativo `dotnet list package --vulnerable`[cite: 5].

### 2. Gestão Segura de Segredos[cite: 5]
Informações altamente críticas do sistema — como as credenciais confidenciais de administração do banco de dados MySQL 8 e as chaves de assinatura criptográfica simétrica dos tokens JWT Bearer (`JWT_PRODUCTION_SECRET`) — foram blindadas e **100% removidas** do arquivo físico `appsettings.json`[cite: 5]. Elas foram configuradas de forma criptografada nos **GitHub Secrets** e são injetadas em variáveis de ambiente dinâmicas estritamente em memória durante a execução do pipeline de testes no servidor[cite: 5].

### 3. Mini Simulação de Resiliência a Incidentes[cite: 5]
O ecossistema foi submetido a uma simulação controlada baseada em engenharia de resiliência corporativa[cite: 5]:
* **Cenário:** Inserção intencional (hardcoded) de uma chave secreta no arquivo `JwtService.cs` durante um commit de teste[cite: 5].
* **Detecção:** O mecanismo automatizado de *Secret Scanning* do GitHub interceptou a falha humana imediatamente no momento do push[cite: 5].
* **Ação Tomada:** O pipeline bloqueou o avanço da esteira, reteve o deploy e disparou alertas automáticos para o time de desenvolvimento corrigir a vulnerabilidade antes que o código fosse integrado[cite: 5].

### 📁 Evidências de Segurança Comprobatórias[cite: 5]
Todos os registros de logs e telas comprobatórias estão salvos fisicamente no repositório dentro do diretório `/devsecops-evidencias`, contendo[cite: 5]:
* `pipeline_sucesso.png`: Print da aba *Actions* validando a execução do pipeline DevSecOps com sucesso (Status: **Passed**) após o bypass seguro das checagens de propriedades auditáveis de terceiros[cite: 5].
* `simulacao_alerta.png`: Print extraído da aba *Security / Secret Scanning* comprovando o bloqueio ativo e o disparo de alerta da chave exposta propositalmente na simulação[cite: 5].

---

## ⚙️ Como Executar a Aplicação

### Pré-requisitos
* .NET 10 SDK
* .NET Aspire Workload (`dotnet workload install aspire`)
* Instância ativa do MySQL 8

### Configuração de Ambiente Local
Crie o arquivo local `MissionClear.Api/appsettings.Development.json` (certificado no `.gitignore` para nunca ser versionado):

```json
{
  "ConnectionStrings": {
    "mysql": "Server=localhost;Database=missionclear;User=root;Password=SUA_SENHA;"
  },
  "Jwt": {
    "Secret": "sua_chave_ficticia_com_mais_de_32_caracteres_aqui",
    "Issuer": "MissionClear",
    "Audience": "MissionClear"
  }
}


# Inicialização via .NET Aspire
dotnet run --project MissionClear.AppHost

# Executar a verificação manual de dependências vulneráveis do Módulo de Cibersegurança
dotnet list package --vulnerable