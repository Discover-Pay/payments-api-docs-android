# Credenciais para Integração

Para realizar a integração entre aplicativos e a solução de pagamento da PayStore, é necessário adquirir algumas credenciais. As credenciais são geradas ao realizar a publicação do aplicativo no Portal da PayStore, através da opção Aplicativos -> Publicados -> Detalhes da aplicação. Serão exibidos o APLICATION_ID e o SECRET_TOKEN. Estas informações devem ser adicionadas ao aplicativo em desenvolvimento que deseja realizar a integração de pagamento.

### As credenciais para o ambiente de desenvolvimento são:
```csharp
public static final String TEST_APPLICATION_ID = "0";
public static final String TEST_SECRET_TOKEN = "000000000000000000000000";
```

A classe [**CredentialsUtils.java**](https://github.com/Discover-Pay/payments-api-demo-android) presente no projeto [**demo**](https://github.com/Discover-Pay/payments-api-demo-android) mostra uma forma de implementar as credenciais que serão usadas no request do pagamento através da classe PaymentRequestV2.java que está presente na lib de integração [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars). Para mais detalhes, veja o projeto demo.


[[Voltar APIs]](./README.md)
