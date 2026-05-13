# SGO - Sistema de Gestão das Olimpíadas

## Participantes
* **Davi Nunes Carvalho**
* **Joao Victor Russo Marquito**

## Descrição do Sistema
O Sistema de Gestão das Olimpíadas (SGO) é uma solução robusta para o gerenciamento completo de eventos esportivos de grande escala. O sistema permite o controle de competições, atletas, delegações e infraestrutura, garantindo a integridade dos dados e a fluidez dos processos olímpicos.

### Regras de Negócio Implementadas
1.  **Cadastro de competições**: Permite registrar a modalidade, data, horário, local e gerenciar a lista de atletas inscritos.
2.  **Inscrição de atletas**: Gerencia a participação de atletas de diferentes países, garantindo que cada atleta represente apenas um país por modalidade.
3.  **Alocação de locais**: Sistema de agendamento que evita conflitos de horário, permitindo apenas uma competição por local simultaneamente.
4.  **Controle de resultados**: Registro oficial dos vencedores (ouro, prata e bronze) após o término das competições.
5.  **Relatórios de medalhas**: Geração automática de quadros de medalhas para visualização do desempenho por país.

## Histórias de Usuário (User Stories)
*   **US01**: Como Organizador, quero cadastrar competições (modalidade, data, horário, local) para estruturar o calendário do evento.
*   **US02**: Como Atleta, quero me inscrever em modalidades representando meu país para participar das provas.
*   **US03**: Como Gestor de Infraestrutura, quero alocar locais para as provas garantindo que não haja sobreposição de horários.
*   **US04**: Como Árbitro, quero registrar os resultados oficiais para que o pódio e as medalhas sejam atribuídos corretamente.
*   **US05**: Como Público/Admin, quero visualizar o relatório de medalhas por país para acompanhar o ranking olímpico.

---

## Modelagem UML

### 1. Diagrama de Caso de Uso
Focado nas interações entre os atores (Admin, Organizador, Atleta e Sistema) e os principais serviços do SGO.

**Código PlantUML:**
```puml
@startuml
left to right direction

skinparam shadowing false
skinparam packageStyle rectangle
skinparam actorStyle awesome

skinparam usecase {
    BackgroundColor #FDFDFD
    BorderColor #444
    ArrowColor #444
}

title Sistema de Gestão das Olimpíadas (SGO)

actor Administrador as Admin
actor Organizador as Org
actor Atleta as Atl
actor "Sistema" as Sys <<Automático>>

rectangle "SGO - Sistema de Gestão das Olimpíadas" {

    package "Gerenciamento de Competições" {
        usecase "Cadastrar Competição" as UC1
        note bottom of UC1 : Inclui modalidade, data,\nhorário e local.
    }

    package "Inscrição de Atletas" {
        usecase "Inscrever em Competição" as UC2
        usecase "Validar Representação de País\n(1 por modalidade)" as UC3
        
        UC2 .> UC3 : <<include>>
    }

    package "Alocação de Locais" {
        usecase "Alocar Local" as UC4
        usecase "Verificar Conflito de\nHorário e Local" as UC5
        
        UC4 .> UC5 : <<include>>
    }

    package "Controle de Resultados" {
        usecase "Registrar Resultados\nda Competição" as UC6
        usecase "Definir Pódio\n(1º, 2º e 3º lugares)" as UC7
        
        UC6 .> UC7 : <<include>>
    }

    package "Relatórios" {
        usecase "Gerar Relatório de\nQuadro de Medalhas" as UC8
    }
}

Org --> UC1
Org --> UC4
Org --> UC6
Atl --> UC2
Admin --> UC8
Admin --> UC1

UC3 <-- Sys
UC5 <-- Sys
@enduml
```

**Visualização:**
<img width="700px" src="https://github.com/Davii13/Sistema-de-Gest-o-das-Olimp-adas-SGO-/blob/main/imagens/diagrama-de-caso-de-uso.png?raw=true"/>

---

### 2. Diagrama de Classes
Representa a estrutura lógica do sistema, atributos, métodos e relacionamentos entre as entidades principais.

**Código PlantUML:**
```puml
@startuml
skinparam classAttributeIconSize 0
skinparam linetype ortho

package "Domínio de Delegações" {
    class Pais {
        - siglaCOI: String
        - nomeOficial: String
        - continente: String
        + getTotalOuro(): Integer
        + getTotalPrata(): Integer
        + getTotalBronze(): Integer
    }

    class Atleta {
        - numeroPassaporte: String
        - nomeCompleto: String
        - genero: String
        - dataNascimento: LocalDate
        + verificarElegibilidade(): Boolean
    }
}

package "Processamento de Inscrições" {
    class Participacao {
        - numeroInscricao: String
        - dataRegistro: LocalDateTime
        - aprovadoCOI: Boolean
        + confirmarParticipacao(): void
        + validarUnicidadePaisModalidade(): Boolean
    }
}

package "Gestão de Eventos e Infraestrutura" {
    class Modalidade {
        - id: Integer
        - nomeDesporto: String
        - categoria: String
        - exigeEquipamento: Boolean
        + validarRegras(): Boolean
    }

    class Local {
        - idLocal: Integer
        - nomeArena: String
        - capacidadeMaxima: Integer
        - tipoPiso: String
        + checarDisponibilidade(inicio: LocalDateTime, fim: LocalDateTime): Boolean
        + reservarTurno(competicao: Competicao): void
    }

    class Competicao {
        - codigoEvento: String
        - fase: String
        - inicioAgendado: LocalDateTime
        - fimAgendado: LocalDateTime
        - statusAtual: String
        + iniciarProva(): void
        + finalizarProva(): void
        + emitirSumario(): String
    }
}

package "Apuragem e Relatórios" {
    class Resultado {
        - idRegistro: String
        - dataCerimonia: LocalDateTime
        - validadoAntidoping: Boolean
        - quebraDeRecorde: Boolean
        + homologarResultados(): void
        + atualizarQuadroGeral(): void
    }
}

Pais "1" <-down- "1..*" Atleta
Atleta "1" -right- "0..*" Participacao
Participacao "*" -right- "1" Competicao
Competicao "*" -down-> "1" Modalidade
Competicao "*" -up-> "1" Local
Competicao "1" *-down- "0..1" Resultado
Resultado -left-> "1" Atleta : medalhistaOuro
Resultado -left-> "1" Atleta : medalhistaPrata
Resultado -left-> "1" Atleta : medalhistaBronze
@enduml
```

**Visualização:**
<img width="700px" src="https://github.com/Davii13/Sistema-de-Gest-o-das-Olimp-adas-SGO-/blob/main/imagens/diagrama-de-classes.png?raw=true"/>

---

### 3. Diagrama de Pacotes
Organização lógica dos módulos do sistema para separação de responsabilidades.

**Código PlantUML:**
```puml
@startuml
package "Domínio de Delegações" {
    class Pais
    class Atleta
}

package "Processamento de Inscrições" {
    class Participacao
}

package "Gestão de Eventos e Infraestrutura" {
    class Modalidade
    class Local
    class Competicao
}

package "Apuragem e Relatórios" {
    class Resultado
    class RelatorioMedalhas
}
@enduml
```

**Visualização:**
<img width="700px" src="https://github.com/Davii13/Sistema-de-Gest-o-das-Olimp-adas-SGO-/blob/main/imagens/diagrama-de-pacotes.png?raw=true"/>

---

### 4. Diagrama de Componentes
Arquitetura de microsserviços mostrando a interação entre Interface, Gateway e os módulos de negócio.

**Código PlantUML:**
```puml
@startuml
skinparam componentStyle rectangle
title SGO - Diagrama de Componentes

component "Interface de Usuário" as UI
component "API Gateway" as Gateway

package "Serviços Core" {
    component "Módulo de Inscrições" as MS_Insc
    component "Módulo de Alocação" as MS_Aloc
    component "Módulo de Competições" as MS_Comp
    component "Módulo de Resultados" as MS_Res
    component "Módulo de Relatórios" as MS_Rel
}

database "DB Inscrições" as DB_I
database "DB Geral" as DB_G

UI --> Gateway
Gateway --> MS_Insc
Gateway --> MS_Aloc
Gateway --> MS_Comp
Gateway --> MS_Res
Gateway --> MS_Rel

MS_Insc --> DB_I
MS_Rel ..> MS_Res : "Consome dados"
@enduml
```

**Visualização:**
<img width="700px" src="https://github.com/Davii13/Sistema-de-Gest-o-das-Olimp-adas-SGO-/blob/main/imagens/diagrama-de-componentes.png?raw=true"/>

---

### 5. Diagrama de Implantação
Representação da infraestrutura física e distribuição dos componentes.

**Código PlantUML:**
```puml
@startuml
node "Dispositivo do Cliente" <<device>> as cliente {
    node "Navegador Web" <<execution environment>> {
        artifact "Interface SGO (React/Next.js)" as front
    }
}

node "Servidor de Borda" <<server>> as borda {
    component "Load Balancer (NGINX)" as lb
}

node "Servidor de Aplicação" <<server>> as app_server {
    component "SGO Backend (Spring Boot)" as api
}

node "Servidor de Banco de Dados" <<server>> as db_server {
    database "SGO Database (MySQL)" as db
}

cliente -down-> lb : HTTPS
lb -down-> api : HTTP
api -down-> db : JDBC
@enduml
```

**Visualização:**
<img width="700px" src="https://github.com/Davii13/Sistema-de-Gest-o-das-Olimp-adas-SGO-/blob/main/imagens/diagrama-de-implantação.png?raw=true"/>