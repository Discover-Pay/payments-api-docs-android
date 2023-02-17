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

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `value` | `BigDecimal` | Não | Valor do pagamento solicitado. Caso não seja preenchido (null), a interface solicitará o valor ao operador. |
| `additionalValueType` | `AdditionalValueType` | Não | Tipo de valor adicional (Cashback, TIP, etc.). Se não estiver preenchido (nulo), deve-se ignorar o campo "additionalValue". ‘AdditionalValueType’ deve admitir, para FastTrack, apenas o valor CASHBACK. Depois de ler o cartão, se o AdditionalValueType informado não for compatível com produto banner do Cartão, o terminal exibe um erro na tela e finaliza a transação. Para evitar que esse erro ocorra, é recomendado usar este campo apenas junto com um "productShortName", que deve ser preenchido cmo um produto que suporta o uso do tipo de valor adicional em questão. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `additionalValue` | `BigDecimal` | Não | Valor adicionado ao valor da transação. Se "additionalValueType" for relatado e "additionalValue" não foi preenchido (nulo) ou é igual a 0 (zero), a interface irá pedir ao operador o valor adicional. Se "additionalValueType" for preenchido e "additionalValue" tiver um valor mais alto do que 0 (zero), a interface não pedirá ao operador o valor adicional. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `paymentTypes` | `List<PaymentType>` | Não | Tipos de pagamentos (Débito, Crédito, Voucher, etc.) permitidos para este pagamento. Caso seja vazio, ou seja, null, significa que todos os tipos são permitidos. Caso contenha apenas um, este tipo será o utilizado (se possível) e não será perguntado nada ao operador. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `installments` | `Integer` | Não | Quantidade de parcelas. Usado apenas para tipos de pagamentos que suportem parcelamento e neste caso é obrigatório. Valor deve ser entre 2 e 99. |
| `accountTypeId` | `String` | Não | Tipo de conta. Se não for preenchido (nulo), a interface pode perguntar ao operador o tipo de conta, dependendo da configuração do produto principal associado ao cartão usado na transação. Depois de ler o cartão, se o accountTypeId inserido não existir na cnofiguração do produto de bandeira do Cartão, o terminal exibe um erro na tela e finaliza a transação. Para evitar que esse erro ocorra, é recomendado usar este campo apenas junto com um "productShortName", onde deve constar um produto que suporta o uso do tipo de conta. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `planId` | `String` | Não | Identificação do plano de pagamento. Pode ter um ou dois caracteres, a depender da regra da adquirente selecionada. Se não for preenchido (nulo), a interface pode solicitar o plano para a operadora, de acordo com a configuração do produto de bandeira associado ao cartão usado na transação. Depois de ler o cartão, se o planId relatado não for compatível com o número de parcelas (capturado no terminal ou informado no parâmetro "Parcelas") e com a configuração do produto de bandeira do cartão (observando as configurações planCondition, planType e planList), o terminal mostra um erro no tela e finaliza a transação. A fim de evitar que esse erro ocorra, é recomendado usar este campo apenas junto com um "productShortName", onde deve constar um produto que apóia o plano referido. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `appTransactionId` | `String` | Sim | Identificador da transação integrada para o software. O Identificador referido é aquele utilizado na aplicação que originou a solicitação de pagamento. Não deve se repetir. |
| `ApplicationInfo.credentials` | `Credentials` | Sim | Credenciais da aplicação que está solicitando a operação, conforme cadastro na PayStore. Basicamente, trata-se da identificação da aplicação e o token de acesso. |
| `ApplicationInfo.softwareVersion` | `String` | Sim | Versão da aplicação que está solicitando o pagamento. |
| `showReceiptView` (DEPRECATED) | `Boolean` | Não | A Solução irá utilizar o valor dos parâmetros `printMerchantReceipt` e `printCustomerReceipt` para executar a impressão depois que a [`confirmação`](#confirmpayment) for executada. |
| `printMerchantReceipt` | `Boolean` | Não | Indica se o comprovante do estabelecimento deve ser impresso ou não depois da [`confirmação`](#confirmpayment) da transação. O valor padrão é _true_, isto é, o comprovante é impresso. |
| `printCustomerReceipt` | `Boolean` | Não | Indica se o comprovante do cliente deve ser impresso ou não depois da [`confirmação`](#confirmpayment) da transação. O valor padrão é _true_, isto é, o comprovante é impresso. |
| `tokenizeCard` | `Boolean` | Não | Indica se deve ser feita ou não a tokenização do cartão após a aprovação do pagamento ou não. O valor padrão é false, isto é, não será feita a tokenização. |
| `tokenizeEmail` | `String` | Se tokenizeCard for true, sim, caso contrário, não. | E-mail do portador do cartão. Se “tokenizeCard” for false, este parâmetro é ignorado. |
| `tokenizeNationalDocument` | `String` | Não | CPF ou CNPJ do portador do cartão. Se “tokenizeCard” for false, este parâmetro é ignorado. Se for true, mas não for informado esse parâmetro, então a chamada à API de criação de token no e-commerce também não o utilizará. |
| `productShortName` | `String` | Não | Identificação resumida do produto de bandeira do cartão. Depois de ler o cartão e identificar o produto de bandeira, se ele não corresponder ao productShortName referido, o terminal exibe um erro na tela e finaliza a transação. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1, o terminal exibe um erro caso o campo seja informado e este valor corresponda a um produto cujo campo `acquirerParams.allowQRCode` seja false; Ou o campo `acquirerParams.qrPaymentMethodId` não esteja configurado; Ou se o produto existir, mas não estiver habilitado no terminal. |
| `note` | `String` | Não | Texto adicional que é inserido como Nota. (pode ser o número da fatura) |
| `dni` | `String` | Não | Número do Documento. Para pagamentos de QRCode estático não considerar este parâmetro quando `operationMethodAllowed` = 1 |
| `operationMethodAllowed` | `Integer` | Sim | Indica o método de operação de pagamento, anulação e devolução. Admita os seguintes valores: 0 - Apenas com cartão físico (lido ou digitado); 1 - Somente com QRCode. |
| `allowBenefit` (OBSOLETO) | `Boolean` | Não | Indica se o QRCode deve ser gerado com as opções do produto associadas aos benefícios. O valor padrão é 'verdadeiro', ou seja, os benefícios serão adicionados. |


[[Voltar]](./README.md)

