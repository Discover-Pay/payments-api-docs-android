# Integração com Estorno

Uma das formas de se integrar com a aplicação de pagamentos  da DiscoverPay é via [**IPC**](https://developer.android.com/guide/components/aidl.html). Para isto, é fornecida uma biblioteca, a [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário a ser usado para tais chamadas.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento) e o estorno. O pagamento pode ser realizado com um valor pré-definido ou com um valor em aberto, a ser digitado pelo operador do terminal. Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagamentos, é fornecida a interface **PaymentClient**.


## Fluxo de Estorno


![Default](https://github.com/Discover-Pay/payments-api-docs-android/blob/main/docs/api/assets/fluxo_estorno.png)

| Passos | Sucesso | Erro |
| --- | --- | --- |
| 1.Solicitação de estorno do pagamento | O estorno do pagamento foi realizado e seu status é `Estornado` | O estorno do pagamento não foi realizado. A resposta contém informações do erro. |
| 2.Resposta solicitação de estorno do pagamento | A resposta contém informações do estorno do pagamento realizado. | A resposta contém informações do erro da solicitação. |
| 3.Solicitação de desfazimento do estorno | Desfazimento realizado, seu status é `Desfeita`. | A resposta contém informações do erro da solicitação. |
| 4.Resposta do desfazimento do estorno | A resposta contém informações do desfazimento realizado. | A resposta contém informações do erro da solicitação. |


O estorno só é finalizado quando existe uma confirmação ou um desfazimento. Em caso de confirmação, o comprovante será impresso.

> :warning: O método PaymentClient.Bind(_callback) deve ser chamado, obrigatoriamente, antes de chamar qualquer método da Integração de Pagamento. O **bind é assíncrono**, ou seja, a próxima linha após o bind() será executada antes de receber a sua resposta, por isso garanta que, antes de chamar os métodos de integração, o bind esteja **conectado**.
