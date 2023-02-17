# Índice
- [Arquitetura](docs/architecture.md)
- [Pré-requisitos](docs/prerequisites.md)
- [Terminais Homologados](docs/pos.md)
- [Referências API](docs/api/README.md)


### Bem-vindo
Documentação da Integração Android para pagamentos DiscoverPay
Uma das formas de se integrar com a aplicação de pagamentos da DiscoverPay é via [**IPC**](https://developer.android.com/guide/components/aidl.html). Para isto, é fornecida uma biblioteca, a [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário a ser usado para tais chamadas.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento) e o estorno. O pagamento pode ser realizado com um valor pré-definido ou com um valor em aberto, a ser digitado pelo operador do terminal. Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos da DiscoverPay pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagamentos, é fornecida a interface PaymentClient presente na biblioteca.


![Default](https://github.com/Discover-Pay/payments-api-docs-android/blob/main/assets/paystore.png)


### Início rápido
Veja os pré-requisitos para iniciar o seu desenvolvimento [*aqui*](docs/prerequisites.md)

### O que esperar?
* Fácil Integração para realizar pagamentos.

* Exemplos de projetos com tecnologias diferentes realizando a integração.

* Customizações de layouts da sua empresa e marca nas telas e comprovantes dos terminais.

### Aplicações de Exemplo
#### App Demo Android

Para facilitar sua integração, criamos um projeto [**demo**](https://github.com/Discover-Pay/payments-api-demo-android) desenvolvido em Android já integrado com app Pagamentos. Explore esse projeto para visualizar a integração em ação.
