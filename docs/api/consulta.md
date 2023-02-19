
# Consulta de Pagamentos via API

Uma das formas de se integrar com a aplicação de pagamentos da Phoebus é via [**IPC**](https://developer.android.com/guide/components/aidl.html). Para isto, é fornecida uma biblioteca, a [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário para realizar as chamadas de integração.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento). Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagamentos, é fornecida a interface **PaymentClient**.
