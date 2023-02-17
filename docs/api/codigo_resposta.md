# Códigos de Resposta da Integração

| Código | Descrição | Operações |
| --- | --- | --- |
| 01  | Transação negada pela adquirente. | `startPayment` e `startPaymentV2` |
| 02  | Transação negada pelo cartão. | `startPayment` e `startPaymentV2` |
| 03  | Operação cancelada pelo operador. | `startPayment`, `startPaymentV2`, `reversePayment` e `reversePaymentV2` |
| 04  | Pagamento não encontrado. | `confirmPayment`, `cancelPayment`, `reversePayment`, `reversePaymentV2` e `cancelReversePayment` |
| 05  | Operação não disponível na adquirente. | `cancelReversePayment` |
| 06  | Problema na comunicação com o aplicativo de pagamento. | `startPayment`, `startPaymentV2`, `reversePayment`, `reversePaymentV2`, `cancelPayment` e `confirmPayment` |
| 07  | Problema de comunicação com a adquirente. | Todas |
| 08  | Credenciais Inválidas. | `startPayment`, `startPaymentV2`, `reversePayment` e `reversePaymentV2` |
| 09  | Aplicativo de Pagamentos não possui permissões para continuar. | `startPayment`,`startPaymentV2`, `reversePayment` e `reversePaymentV2` |
| 10  | Terminal Bloqueado. | `startPayment`, `startPaymentV2`, `reversePayment` e `reversePaymentV2` |
| 11  | Pagamento bloqueado, pois existe transação pendente. | `startPayment`, `startPaymentV2`, `reversePayment` e `reversePaymentV2` |
| 12  | Tema inválido | `setTheme` |
| 13  | SERVIÇO OCUPADO, AGUARDE | `confirmPayment` `cancelPayment` |
| 14  | Erro ao definir aplicativo principal | `setMainApp` |
| 15  | A aplicação principal já está definida por campos adicionais | `setMainApp` |
| 16  | Pagamento habilitado apenas via API | `terminalFilterPayment` |
| 17  | Houve um problema ao extrair ou carregar os dados. | `startExtraction` |
| 18  | Inicialização falhou. | `startInitialization` |
| 19  | Um erro ocorreu ao checar as notificações. | `getNotifications` |
| 20  | Houve um problema ao realizar o teste de comunicação. | `startEchoTest` |
| 21  | Ocorreu um erro durante o fechamento de lote. | `closeBatch` |
| 22  | Operação não permitida. | Todas |
| 24  | Transação com QRCode está pendente | `startPayment`, `startPaymentV2`, `reversePayment` e `reversePaymentV2` |
| 99  | ERRO INTERNO. | Todas |
