# ProjetoBanco

Projeto de Sistema Bancário Simplificado
Este documento detalha a estrutura e as funcionalidades de um sistema bancário desenvolvido em Python, seguindo os princípios da programação orientada a objetos, tratamento de exceções e persistência de dados.
1. Definição do Sistema (Descrição do Problema a ser Resolvido)
O projeto visa simular as operações fundamentais de um sistema bancário. O problema a ser resolvido é a criação, gerenciamento e persistência de contas bancárias de clientes, permitindo transações básicas como saques, depósitos e transferências.
O sistema foi modelado para ser robusto e extensível, abordando os seguintes requisitos:
 * Gerenciamento de Clientes e Contas: O sistema deve ser capaz de registrar clientes com seus dados (nome e CPF) e associá-los a contas bancárias.
 * Diferentes Tipos de Conta: Deve suportar múltiplos tipos de conta com comportamentos distintos. Neste caso, foram implementadas a Conta Corrente e a Conta Poupança, cada uma com suas próprias regras para taxas e rendimentos.
 * Operações Bancárias: As funcionalidades essenciais como depositar, sacar, transferir fundos entre contas e consultar extrato devem estar disponíveis.
 * Segurança e Validação: O sistema precisa validar as operações para prevenir inconsistências, como saques com valor superior ao saldo disponível, depósitos de valores negativos ou operações em contas inexistentes.
 * Persistência de Dados: As informações das contas e clientes não devem ser perdidas ao encerrar o programa. Os dados devem ser armazenados de forma permanente em um banco de dados.
 * Interface com o Usuário: Deve haver uma maneira para o usuário interagir com o sistema, realizando as operações bancárias de forma intuitiva através de um menu de linha de comando (CLI).
2. Implementação do Código (Funcionalidades Desenvolvidas)
O código é estruturado em módulos lógicos que representam os diferentes componentes do sistema bancário.
A. Modelagem Orientada a Objetos
A solução é construída sobre um sólido modelo de classes que representam as entidades do mundo real.
 * Cliente: Uma classe simples que encapsula os dados do cliente (nome e cpf). O uso de propriedades (@property) garante que os atributos sejam acessados de forma controlada.
 * Conta (Classe Abstrata Base):
   * Serve como um "contrato" para todas as contas, definindo a estrutura e o comportamento comuns (número, cliente, saldo, métodos depositar, transferir, extrato).
   * Utiliza abc.ABC para se tornar uma classe abstrata e @abstractmethod para o método sacar, forçando que toda classe filha (concreta) implemente sua própria lógica de saque.
   * Inclui um método de classe (@classmethod) total_contas para rastrear o número total de contas criadas.
 * ContaCorrente e ContaPoupanca:
   * Herdeiras da classe Conta, representam os tipos específicos de contas.
   * Implementam o método sacar e especializam os métodos depositar (a ContaCorrente cobra uma taxa de R$ 0,10 por depósito) e atualiza (aplicando rendimentos com multiplicadores diferentes), demonstrando o conceito de polimorfismo.
B. Tratamento Robusto de Exceções
Para lidar com erros de forma clara e específica, foi criada uma hierarquia de exceções customizadas.
 * ErroDeConta: Classe base para todas as exceções do domínio bancário.
 * SaldoInsuficienteError: Lançada quando um saque ou transferência não pode ser concluído por falta de fundos.
 * OperacaoInvalidaError: Lançada para operações logicamente incorretas (ex: depositar valor negativo, transferir para um objeto que não é uma conta).
 * ContaNaoEncontradaError: Lançada quando se tenta operar sobre uma conta que não existe no banco.
Essa abordagem torna o fluxo de controle, especialmente na interface do usuário, muito mais limpo e legível.
C. Persistência de Dados com SQLite
A classe Banco é responsável por gerenciar o ciclo de vida dos objetos Conta e persistir seus dados.
 * Banco de Dados: Utiliza o SQLite 3, um sistema de banco de dados leve e embutido no Python, eliminando a necessidade de um servidor de banco de dados externo.
 * __setup_database(): Um método robusto que cria as tabelas clientes e contas com a estrutura correta (incluindo chave estrangeira) se elas não existirem. Ele também contém uma lógica para verificar e recriar as tabelas caso uma estrutura antiga seja detectada, garantindo a integridade dos dados.
 * Mapeamento Objeto-Relacional (ORM) Simplificado: Os métodos adicionar_conta, buscar_conta, remover_conta e atualizar_saldo traduzem as operações com objetos Python para comandos SQL (INSERT, SELECT, DELETE, UPDATE), abstraindo a complexidade do banco de dados.
 * Reconstrução de Objetos: O método buscar_conta não apenas lê os dados do banco, mas também reconstrói a instância da classe correta (ContaCorrente ou ContaPoupanca) com base na informação de "tipo" armazenada, mantendo o polimorfismo do sistema.
D. Protocolos de Contêiner
A classe Banco implementa os protocolos de contêiner do Python, tornando seu uso mais intuitivo e "pythônico".
 * __len__(self): Permite o uso da função len(banco) para obter o número total de contas cadastradas.
 * __iter__(self): Permite iterar diretamente sobre o objeto banco (ex: for conta in banco:), que, por baixo dos panos, busca cada conta no banco de dados e a retorna como um objeto.
E. Interface de Linha de Comando (CLI)
A função menu() serve como a camada de apresentação do sistema.
 * Loop Interativo: Apresenta um menu de opções ao usuário em um loop contínuo.
 * Entrada de Dados: Captura as informações fornecidas pelo usuário para realizar as operações.
 * Interação com o Banco: Atua como um controlador, chamando os métodos apropriados da classe Banco para executar as ações solicitadas (criar conta, depositar, etc.).
 * Feedback ao Usuário: Utiliza blocos try...except para capturar as exceções customizadas lançadas pela lógica do sistema e exibir mensagens de erro amigáveis e informativas para o usuário.
