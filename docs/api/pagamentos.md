# Integração com Pagamentos V2


ma das formas de se integrar com a aplicação de pagamentos da Phoebus é via [**IPC**]. Para isto, é fornecida uma biblioteca, a  [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário a ser usado para tais chamadas.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento) e o estorno. O pagamento pode ser realizado com um valor pré-definido ou com um valor em aberto, a ser digitado pelo operador do terminal. Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos da Phoebus pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagam


![Default](https://github.com/Discover-Pay/payments-api-docs-android/blob/main/docs/api/assets/fluxo_pagamento.png)

Passos | Sucesso | Erro
------------ | ------------- | -------------
**1.Solicitação de pagamento** | O pagamento foi realizado e seu status é Pendente | O pagamento não foi realizado. A resposta contém informações do erro.. | [optional] 
**2.Resposta solicitação de pagamento** | A resposta contém informações do pagamento realizado. | A resposta contém informações do erro da solicitação. | [optional] 
**3.Solicitação de confirmação** | Confirmação realizada, seu status é Confirmada, não pode ser desfeita.| Confirmação não realizada. A resposta contém informações do erro. | [optional] 
**4.Resposta da confirmação** | A resposta contém informações da confirmação realizada. Impressão do Comprovante | A resposta contém informações do erro da solicitação. Nesse ponto pode ser enviado o desfazimento. | [optional] 
**5.Solicitação de desfazimento** | Desfazimento realizado, seu status é Desfeita. Não pode ser confirmada. | A resposta contém informações do erro da solicitação. O status continua Pendente. | [optional] 
**6.Resposta do desfazimento** | A resposta contém informações do desfazimento realizado.** | A resposta contém informações do erro da solicitação. | [optional] 


[[Voltar]](./README.md)

O pagamento só é finalizado quando existe uma confirmação ou um cancelamento (desfazimento). Em caso de confirmação, o comprovante estará disponível para impressão ou envio por SMS/e-mail, dependendo das funcionalidades do terminal
