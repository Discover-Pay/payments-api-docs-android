# Integração com Pagamentos V2 


ma das formas de se integrar com a aplicação de pagamentos da Phoebus é via [**IPC**]. Para isto, é fornecida uma biblioteca, a  [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário a ser usado para tais chamadas.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento) e o estorno. O pagamento pode ser realizado com um valor pré-definido ou com um valor em aberto, a ser digitado pelo operador do terminal. Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos da Phoebus pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagam


![Default](https://github.com/Discover-Pay/payments-api-docs-android/blob/main/docs/api/assets/fluxo_pagamento.png)

Passos | Sucesso | Erro
------------ | ------------- | -------------
**1.Solicitação de pagamento** | O pagamento foi realizado e seu status é Pendente | O pagamento não foi realizado. A resposta contém informações do erro. | [optional] 
**2.Resposta solicitação de pagamento** | A resposta contém informações do pagamento realizado. | A resposta contém informações do erro da solicitação. | [optional] 
**3.Solicitação de confirmação** | Confirmação realizada, seu status é Confirmada, não pode ser desfeita.| Confirmação não realizada. A resposta contém informações do erro. | [optional] 
**4.Resposta da confirmação** | A resposta contém informações da confirmação realizada. Impressão do Comprovante | A resposta contém informações do erro da solicitação. Nesse ponto pode ser enviado o desfazimento. | [optional] 
**5.Solicitação de desfazimento** | Desfazimento realizado, seu status é Desfeita. Não pode ser confirmada. | A resposta contém informações do erro da solicitação. O status continua Pendente. | [optional] 
**6.Resposta do desfazimento** | A resposta contém informações do desfazimento realizado.** | A resposta contém informações do erro da solicitação. | [optional] 

O pagamento só é finalizado quando existe uma confirmação ou um cancelamento (desfazimento). Em caso de confirmação, o comprovante estará disponível para impressão ou envio por SMS/e-mail, dependendo das funcionalidades do terminal


## Métodos
Assinatura | Descrição
------------------------------------------------------------------------ | ------------------------------------------------------------------------------
**void startPaymentV2(PaymentRequestV2 paymentRequest, PaymentCallback paymentCallback)** | Realiza o processo de autorização de pagamento. | [optional] 
**void confirmPayment(String paymentId, PaymentCallback paymentCallback)** | Confirma uma autorização de pagamento realizada anteriormente. | [optional] 
**void cancelPayment(String paymentId, PaymentCallback paymentCallback)** | Desfaz uma autorização de pagamento realizada anteriormente. | [optional] 

## startPaymentV2()
Este método deve ser chamado quando se deseja fazer uma solicitação de autorização de pagamento. Durante sua execução, os dados do pagamento serão validados, informações adicionais serão solicitadas ao operador (e.g. senha e CVV), e a autorização junto à adquirente será feita.

# Parâmetros
Nome | Tipo | Obrigatório | Descrição 
-----|------|-------------|----------|
**request** |PaymentRequestV2|Sim|Objeto de transferência de dados que conterá as informações da requisição do pagamento. Note que nem todos os parâmetros são obrigatórios.
**callback** |PaymentCallback|Sim|Interface que será executada para notificações de sucesso ou erro do processo de pagamento.

### Detalhe dos Parâmetros
request (PaymentRequestV2)

Nome | Tipo | Obrigatório | Descrição 
-----|------|-------------|----------|
**value** |BigDecimal|Não|Valor do pagamento solicitado. Caso não seja preenchido (null), a interface solicitará o valor ao operador.
**additionalValueType** |AdditionalValueType|Não|Tipo de valor adicional (Cashback, TIP, etc.). Se não estiver preenchido (nulo), deve-se ignorar o campo "additionalValue". ‘AdditionalValueType’ deve admitir, para FastTrack, apenas o valor CASHBACK. Depois de ler o cartão, se o AdditionalValueType informado não for compatível com produto banner do Cartão, o terminal exibe um erro na tela e finaliza a transação. Para evitar que esse erro ocorra, é recomendado usar este campo apenas junto com um "productShortName", que deve ser preenchido cmo um produto que suporta o uso do tipo de valor adicional em questão. Para pagamentos de QRCode estático não considerar este parâmetro quando operationMethodAllowed = 1



[[Voltar]](./README.md)

