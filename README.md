# Desafio DIO: Documentando a Criação de um Banco de Dados SQL no Azure

## 1. Visão Geral do Projeto
Este repositório documenta a execução do Desafio de Projeto da [DIO](https://www.dio.me/) sobre a **criação de uma instância de Banco de Dados SQL na plataforma Microsoft Azure**. O objetivo principal é aplicar os conceitos de provisionamento de serviços de nuvem e, ao mesmo tempo, praticar a criação de documentação técnica clara e estruturada utilizando Markdown e Git/GitHub.

## 2. Azure SQL: Instância Gerenciada vs. Banco de Dados Único
Antes de iniciar o guia prático, é fundamental entender as opções que o Azure oferece. A escolha entre uma **Instância Gerenciada (Managed Instance)** e um **Banco de Dados SQL (Single Database)** depende diretamente do caso de uso.

* **Banco de Dados SQL do Azure (Single Database)**: É uma opção de banco de dados como serviço (DBaaS) totalmente gerenciada, ideal para **novas aplicações nativas da nuvem**. Ele oferece alta flexibilidade, com modelos de compra baseados em DTU ou vCore, e é otimizado para cenários onde cada banco de dados tem seus próprios recursos.

* **Instância Gerenciada de SQL do Azure (Managed Instance)**: É uma oferta de plataforma como serviço (PaaS) que oferece **quase 100% de compatibilidade com o SQL Server on-premises**. É a escolha perfeita para a **modernização de aplicações existentes (lift-and-shift)**, permitindo migrar bancos de dados do seu ambiente local para a nuvem com o mínimo de alterações. Ele opera dentro de uma VNet (Rede Virtual), garantindo maior isolamento e segurança.

<details>
<summary><strong>Clique para ver uma tabela comparativa detalhada</strong></summary>

| Característica          | Banco de Dados SQL (Single Database)                   | Instância Gerenciada de SQL (Managed Instance)       |
| :---------------------- | :----------------------------------------------------- | :--------------------------------------------------- |
| **Caso de Uso Principal** | Novas aplicações nativas da nuvem                      | Migração de SQL Server (Lift & Shift)                |
| **Compatibilidade** | Subconjunto de recursos do SQL Server                  | Quase 100% compatível com SQL Server                 |
| **SQL Agent** | Não disponível (automatização via Azure Functions, etc.) | **Disponível** |
| **Rede** | Ponto de extremidade público (pode ser restrito)       | Implantação dentro de uma **VNet (isolamento)** |
| **Backup/Restauração** | Backups automáticos, restauração pontual               | Backups automáticos, restauração pontual             |
| **Modelo de Compra** | DTU e vCore                                            | Apenas vCore                                         |
| **Escalabilidade** | Escalabilidade por banco de dados                      | Recursos compartilhados entre bancos                 |
| **CLR (Common Language Runtime)** | Não suportado                                | **Suportado** |
| **Service Broker** | Apenas entre bancos de dados na mesma instância        | **Suportado entre instâncias** |

</details>

## 3. Guia Passo a Passo: Criando a Instância Gerenciada

A seguir, estão os passos detalhados para provisionar uma Instância Gerenciada de SQL no portal do Azure.

**Pré-requisitos:**
* Uma conta ativa do Azure.
* Uma assinatura com permissões para criar recursos.

---

**Passo 1:** No portal do Azure, pesquise por `Azure SQL` na barra de busca superior e selecione o serviço.

**Passo 2:** Na página do Azure SQL, clique em **+ Criar**.

**Passo 3:** Em "Bancos de dados SQL", localize a opção **Instâncias Gerenciadas de SQL** e clique em **Criar**.

![IMAGEM-01: Seleção do tipo de recurso - Instância Gerenciada](./images/01-selecao-recurso.png)

**Passo 4:** Na aba **Básico**, preencha os detalhes do projeto:
* **Assinatura** e **Grupo de Recursos**.
* **Nome da instância gerenciada** (deve ser único globalmente).
* **Região** (escolha a mais próxima de seus usuários).
* **Logon do administrador** e **Senha**.

![IMAGEM-02: Aba de configuração Básica](./images/02-aba-basico.png)

**Passo 5:** Clique em **Configurar Instância Gerenciada** para definir o poder computacional e o armazenamento. Para este desafio, a camada **Uso Geral** com as especificações mínimas é suficiente.

![IMAGEM-03: Configuração de Computação e Armazenamento](./images/03-computacao-armazenamento.png)

**Passo 6:** Na aba **Rede**, configure a rede virtual (VNet). Se não houver uma VNet compatível, o portal permitirá criar uma nova. A Instância Gerenciada **precisa** estar em uma sub-rede dedicada. Deixe o tipo de conexão como "Proxy" (padrão).

![IMAGAGEM-04: Aba de configuração de Rede](./images/04-aba-rede.png)

**Passo 7:** Na aba **Configurações adicionais**, você pode definir o `Collation` do banco de dados (geralmente o padrão é suficiente) e o fuso horário.

**Passo 8:** Na aba **Marcas (Tags)**, você pode adicionar metadados para organizar e gerenciar seus recursos. É uma boa prática para controle de custos e governança.

**Passo 9:** Na aba **Revisar + criar**, o Azure validará todas as configurações. Se tudo estiver correto, uma mensagem de "Validação bem-sucedida" será exibida.

![IMAGEM-05: Tela de Revisão e Criação](./images/05-revisar-criar.png)

**Passo 10:** Clique em **Criar** para iniciar o provisionamento. **Atenção:** a criação de uma Instância Gerenciada pode levar várias horas.

**Passo 11:** Após a conclusão, acesse o recurso criado. Na página de visão geral, você encontrará o **Host**, que é o endereço do servidor para se conectar usando ferramentas como o SQL Server Management Studio (SSMS) ou o Azure Data Studio.

![IMAGEM-06: Visão Geral da Instância Gerenciada criada](./images/06-visao-geral-recurso.png)

## 4. Dicas e Boas Práticas (Otimização de Custos)
Gerenciar os custos na nuvem é crucial. Aqui estão algumas dicas importantes para o Azure SQL:

* **Benefício Híbrido do Azure:** Se você já possui licenças do SQL Server com Software Assurance, pode usá-las para obter um desconto significativo no custo da Instância Gerenciada.
* **Dimensionamento Correto (Right-Sizing):** Monitore o uso da CPU e da memória da sua instância e ajuste a camada de serviço (vCores) para atender à demanda real, evitando gastos com recursos ociosos.
* **Reservas de Capacidade:** Para cargas de trabalho estáveis e previsíveis, você pode se comprometer com uma reserva de 1 ou 3 anos para obter descontos ainda maiores em comparação com o modelo de pagamento conforme o uso.
* **Agendamento de Start/Stop:** Em ambientes de desenvolvimento e teste, automatize o desligamento das instâncias fora do horário de trabalho para economizar custos.

## 5. Estrutura da Documentação
Este repositório foi estruturado de forma simples para facilitar a compreensão:

* **`README.md`**: Este arquivo principal, que serve como guia central do projeto.
* **`/images`**: Uma pasta contendo todas as capturas de tela (screenshots) do processo, mantendo o repositório organizado.

## 6. Conclusão
A conclusão deste desafio permitiu aplicar na prática os conhecimentos sobre os serviços de banco de dados do Azure. O processo de criação de uma Instância Gerenciada, embora simples, envolve decisões importantes sobre rede, computação e segurança. Além disso, a tarefa de documentar cada etapa reforça a importância de registrar o processo de forma clara, o que é uma habilidade essencial para qualquer profissional de tecnologia.

## 7. Recursos Úteis
* [Início Rápido: Criar Instância Gerenciada de SQL do Azure](https://learn.microsoft.com/pt-br/azure/azure-sql/managed-instance/instance-create-quickstart)
* [Documentação do GitHub](https://docs.github.com/pt)
* [Guia de Markdown do GitHub](https://docs.github.com/pt/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
