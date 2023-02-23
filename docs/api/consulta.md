
# Consulta de Pagamentos via API

Uma das formas de se integrar com a aplicação de pagamentos da DiscoverPay é via [**IPC**](https://developer.android.com/guide/components/aidl.html). Para isto, é fornecida uma biblioteca, a [**payments-api-x.x.x.x.aar**](https://github.com/Discover-Pay/payments-api-demo-android/tree/main/app/aars), contendo todo o código necessário para realizar as chamadas de integração.

Usando esta API, é possível realizar todo o fluxo de pagamento, ou seja, a confirmação (ou o desfazimento). Além disso, pode-se informar uma lista de tipos de pagamentos (débito, crédito à vista, crédito parcelado, etc.) permitidos.

Ainda que esta integração se dê através de uma API, a aplicação de pagamentos pode exibir informações na interface do terminal, tais como mensagens (e.g., "Insira ou passe o cartão"), ou mesmo solicitar informações do operador (e.g., CVV). Assim sendo, durante a realização de qualquer operação, a aplicação que solicitou a operação não deve interagir com a interface do dispositivo até que a operação seja concluída.

A seguir, temos a especificação detalhada das operações disponíveis.

Para integração com a API de pagamentos, é fornecida a interface **PaymentClient**.

## Integração com Aplicação de Pagamentos via Content Provider
A integração via Content Provider possibilita que outros aplicativos possam consultar informações a respeito de pagamentos efetuados, sendo possível realizar filtros e obter diversos dados dos pagamentos, inclusive sua situação.

Só será permitido listar pagamentos feitos pela própria aplicação que está realizando a consulta.

Declare essa permissão no AndroidManifest.xml do seu Aplicativo para ter acesso ao Content Provider.

```java
<uses-permission android:name="br.com.phoebus.android.payments.provider.READ_PERMISSION" />
```

content://br.com.phoebus.android.payments.provider/payments

URI (Uniform Resource Identifier) para obtenção de informações de pagamentos.

Para realizar o request da consulta de pagamento por período entre datas é necessário adicionar essa dependência.

```java
implementation 'com.jakewharton.threetenabp:threetenabp:1.0.3'
```

## Filtros

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| `status` | `PaymentStatus` | Não | Filtra os pagamentos cuja situação está na lista passada (aceita mais de um valor). |
| `startDate` | `Date` | Não | Filtra os pagamentos cuja data seja maior ou igual ao valor passado. |
| `finishDate` | `Date` | Não | Filtra os pagamentos cuja data seja menor ou igual ao valor passado. |
| `lastUpdateQuery` | `Boolean` | Não | Indica se a consulta será feita com base na data e hora da transação (caso esse campo não seja preenchido ou receba 'false') ou na data e hora da última atualização dela (caso esse campo receba 'true'). |
| `startValue` | `BigDecimal` | Não | Filtra os pagamentos cujo valor seja maior ou igual ao valor passado. |
| `finishValue` | `BigDecimal` | Não | Filtra os pagamentos cujo valor seja menor ou igual ao valor passado. |
| `paymentId` | `String` | Não | Filtra os pagamentos cujo identificador da transação para a aplicação de pagamentos seja o valor passado. |
| `lastDigits` | `String` | Não | Filtra os pagamentos cujos últimos 4 dígitos do PAN do cartão usado na transação seja igual ao valor passado. |
| `applicationId` | `Credentials` | Sim | Identificação da aplicação que está realizando a consulta. |
| `secretToken` | `Credentials` | Sim | Token de acesso da aplicação que está realizando a consulta. |
| `softwareVersion` | `String` | Sim | Versão da aplicação que está solicitando a consulta. |
| `productShortName` | `String` | Não | Identificador de produto, retorna apenas transações de um produto. |
| `ticketNumber` | `String` | Não | ticketNumber gerado pelo terminal para a transação. |
| `batchPend` | `Boolean` | Não | Filtra as transações que ainda estão estão no lote pendente (transações novas). |
| `lastBatch` | `Boolean` | Não | Filtra as transações do último lote fechado. |
| `batchNumber` | `String` | Não | Filtra as transações de um lote. |
| `acquirerResponseCode` | `String` | Não | Filtra transações com base no código de resposta do host. |
| `lastTrx` | `Boolean` | Não | Filtra a última transação aprovada. |
| `trxType` | `String` | Não | Filtra as transações por tipo ("1" - Venda, "2" - Devolução não Referenciada). |
| `note` | `String` | Não | Texto adicional que é inserido como Nota. (pode ser o número da fatura) |
| `dni` | `String` | Não | Número do Documento. |
| `qrId` | `String` | Não | Identificador QrCode gerado pelo terminal de captura. |
| `operationMethod` | `Integer` | Não | Filtra transações segundo a forma de operação. Admita os seguintes valores: 0 - Apenas com cartão físico (ler ou datilografado); 1 - Somente com QRCode. |
| `appTransactionId` | `String` | Não | Identificador da transação integrada para o software. O Identificador referido é aquele utilizado na aplicação que originou a solicitação de pagamento. |

## Retorno
| Nome | Tipo | Descrição |
| --- | --- | --- |
| `id` | `String` | Identificador da transação para a aplicação de pagamentos. Esta é a informação a ser usada para a confirmação e desfazimento. |
| `value` | `BigDecimal` | Valor do pagamento. Este é o valor que foi aprovado pela adquirente. Deve ser validado sempre na resposta, ainda que tenha sido passado como parâmetro, pois há adquirentes que, para algumas situações, aprovam valores diferentes dos solicitados. |
| `paymentType` | `PaymentType` | Tipo de pagamento (Débito, Crédito, Voucher, etc.). |
| `installments` | `Integer` | Quantidade de parcelas do pagamento. |
| `acquirerName` | `String` | Adquirente que autorizou o pagamento. |
| `cardBrand` | `String` | Bandeira do cartão. |
| `cardBin` | `String` | BIN do cartão. |
| `cardPanLast4Digits` | `String` | Últimos 4 dígitos do PAN do cartão. |
| `captureType` | `CaptureType` | Forma de captura do cartão. |
| `status` | `PaymentStatus` | Situação do pagamento. |
| `date` | `Date` | Data/hora do pagamento para a aplicação de pagamentos. |
| `acquirerId` | `String` | Identificador da transação para a adquirente. Este é o identificador que consta no arquivo que a adquirente fornece (EDI). Desta forma, é possível realizar a conciliação do pagamento com a transação integrada. |
| `acquirerResponseCode` | `String` | Código de resposta da adquirente. |
| `acquirerResponseDate` | `String` | Data/hora retornada pela adquirente. |
| `acquirerAuthorizationNumber` | `String` | Número da autorização fornecido pela adquirente (consta no comprovante do cliente Portador do Cartão). |
| `receiptClient` | `String` | Conteúdo do comprovante - via do cliente. |
| `receiptMerchant` | `String` | Conteúdo do comprovante - via do estabelecimento. |
| `additionalValueType` | `AdditionalValueType` | Presente apenas quando existe um valor adicional no contexto da transação executada. |
| `additionalValue` | `BigDecimal` | Presente apenas quando existe um valor adicional no contexto da transação executada. |
| `accountTypeId` | `String` | Presente apenas quando existe um tipo de conta no contexto da transação executada. |
| `planId` | `String` | Presente apenas quando existe um plano no contexto da transação executada. |
| `productShortName` | `String` | Identificador de produto, retorna apenas transações de um produto. |
| `batchNumber` | `String` | Número de lote. |
| `nsuTerminal` | `String` | NSU gerado pelo terminal para a transação. |
| `cardholderName` | `String` | Nome do cliente no cartão. |
| `lastTrx` | `Boolean` | Indica 'true' se é a última transação aprovada, e 'falso' caso contrário. |
| `terminalId` | `String` | Identificação do terminal. |
| `originalValue` | `BigDecimal` | Valor orginal da venda. Presente em pagamentos com QRCode, cujo beneficio foi aplicado ao valor da venda. |
| `appTransactionId` | `String` | Identificador de transação para o aplicativo de pagamento. Esta é a informação que será usada para confirmar e desfazer o pagamento. 

> ℹ️: É possível customizar quais informações estarão presentes na resposta. Para isso, deve ser passado um array de Strings com as colunas desejadas para o método query() do Content Resolver. Caso use a API de acesso ao provider, pode utilizar o método setColumns() do PaymentProviderRequest.


> ℹ️: Para facilitar o uso, são disponibilizadas classes de acesso ao provider:
- PaymentProviderRequest
- PaymentProviderApi
- PaymentContract

## Exemplo
```Java
import androidx.appcompat.app.AppCompatActivity;

import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;

import com.jakewharton.threetenabp.AndroidThreeTen;

import java.math.BigDecimal;

import java.text.SimpleDateFormat;
import java.util.Arrays;

import java.util.Date;
import java.util.List;
import java.util.TimeZone;

import br.com.phoebus.android.payments.api.ApplicationInfo;
import br.com.phoebus.android.payments.api.Credentials;

import br.com.phoebus.android.payments.api.Payment;
import br.com.phoebus.android.payments.api.PaymentClient;
import br.com.phoebus.android.payments.api.PaymentStatus;
import br.com.phoebus.android.payments.api.provider.PaymentContract;
import br.com.phoebus.android.payments.api.provider.PaymentProviderApi;
import br.com.phoebus.android.payments.api.provider.PaymentProviderRequest;

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    Button bt_start;
    private PaymentClient paymentClient;
    public static final String TEST_APPLICATION_ID = "0";
    public static final String TEST_SECRET_TOKEN = "000000000000000000000000";
    public static final String TAG = "TAG_DEMO";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        bt_start = (Button) this.findViewById(R.id.button);
        bt_start.setOnClickListener(this);
        paymentClient = new PaymentClient();
        AndroidThreeTen.init(getApplication());

    }


    @Override
    public void onClick(View view) {
        doExecute();
    }


    public void doExecute() {
        //definindo as credenciais
        Credentials credentials = new Credentials();
        credentials.setApplicationId(TEST_APPLICATION_ID);
        credentials.setSecretToken(TEST_SECRET_TOKEN);

        ApplicationInfo applicationInfo = new ApplicationInfo();
        applicationInfo.setCredentials(credentials);
        applicationInfo.setSoftwareVersion("1.0");

        //criando objeto de request para o payment content provider
        PaymentProviderRequest request = createRequest(applicationInfo);

        //selecionando propriedades retornadas
        String[] columns = new String[]{
                PaymentContract.column.ID,
                PaymentContract.column.VALUE,
                PaymentContract.column.PAYMENT_STATUS,
                PaymentContract.column.PAYMENT_DATE,
                PaymentContract.column.CARD_BRAND,
        };

        try {
            //solicitando a lista de pagamentos
            request.setColumns(columns);
            List<Payment> paymentsList = PaymentProviderApi.create(this).findAll(request);
            //********* Outra forma de realizar a consulta ******************************************
            // Cursor cursor = getApplicationContext().getContentResolver().query(request.getUriBuilder().build(), columns, null, null, null);
            // List<Payment> paymentsList = Payment.fromCursor(cursor);
            //***************************************************************************************

            Toast.makeText(this, paymentsList.size()+"", Toast.LENGTH_LONG).show();

        } catch (Exception e) {
            Toast.makeText(this, e.getMessage(), Toast.LENGTH_LONG).show();
            Log.e(TAG, e.getMessage(), e);
        }
    }

    private PaymentProviderRequest createRequest(ApplicationInfo appInfo) {


        PaymentProviderRequest paymentRequest = new PaymentProviderRequest();
        paymentRequest.setApplicationInfo(appInfo);


        //filtrando pelo paymentId
        paymentRequest.setPaymentId("000000000");

        //filtrando por período
        SimpleDateFormat isoFormat = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS");
        isoFormat.setTimeZone(TimeZone.getTimeZone("UTC"));

        try {

            Date dateStart = isoFormat.parse("2020-04-06T00:00:00.000");
            Date dateFinish =isoFormat.parse("2020-04-06T23:59:59.000");
            paymentRequest.setStartDate(dateStart);
            paymentRequest.setFinishDate(dateFinish);
        } catch (Exception e) {
            Toast.makeText(this, e.getMessage(), Toast.LENGTH_LONG).show();
            Log.e(TAG, e.getMessage(), e);

        }

        //filtrando por faixa de valores de pagamento
        paymentRequest.setStartValue(new BigDecimal("0.00"));
        paymentRequest.setFinishValue(new BigDecimal("1000.00"));

        //filtrando pelos 4 últimos dígitos do cartão
        paymentRequest.setLastDigits("9636");

        //filtrando pelos 4 últimos dígitos do cartão
        List<PaymentStatus> status = Arrays.asList(new PaymentStatus[]{
                PaymentStatus.PENDING,
                PaymentStatus.CONFIRMED,
                PaymentStatus.CANCELLED,
                PaymentStatus.REVERSED
        });

        return paymentRequest;
    }
}
```

## Consultar Campos Adicionais da PayStore
A PayStore utiliza um poderoso recurso de passagem de parâmetros cadastrados no Servidor para serem utilizados pelos terminais, sem que seja necessário alterar a mensageria de Inicialização. Isso traz uma enorme flexibilidade para criação de novas funcionalidades nos apps.

Os Campos Adicionais (também chamados de Parâmetros) são informações que podem ser cadastradas no Portal da PayStore, podendo ser configurados para todos os lojistas e terminais do Facilitador, para um grupo de lojistas específico, para um lojista (afeta todos os terminais daquele lojista) ou para um terminal específico. Essas informações podem ser lidas por um aplicativo através de Providers.

A classe ConfigurationStoreContract é responsável por prover essas informações. Segue, abaixo, um exemplo de sua implementação:
```java
import android.content.ContentResolver;
import android.content.Context;
import android.database.Cursor;
import android.net.Uri;

import br.com.phoebus.android.payments.api.provider.ConfigurationStoreContract;

public class ConfigController {

  private Context context;

  public ConfigController(Context context) {
    this.context = context;
  }

  public String getProperty(String key) {

    Uri.Builder uriBuilder = ConfigurationStoreContract.getUriBuilder();
    uriBuilder.appendQueryParameter(ConfigurationStoreContract.column.KEY, key);
    ContentResolver resolver = this.context.getContentResolver();

    String value = null;
    try (Cursor query = resolver.query(uriBuilder.build(), null, null, null, null)) {

      if (query != null && query.moveToFirst()) {
        value = query.getString(query.getColumnIndex(ConfigurationStoreContract.column.VALUE));
      }
    }
    return value;
  }
}
```

Para utilizar essa classe, basta usar a chave definida previamente nos Campos Adicionais como parâmetro, conforme abaixo:
```java
    ConfigController config = new ConfigController(mContext);
    String conteudoCampoAdicional = config.getProperty("sua_chave");
```
