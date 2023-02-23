# Resposta Broadcast Fechamento de Lote

| Nome | Tipo | Obrigatório | Descrição |
| --- | --- | --- | --- |
| **`succeeded`** | `Boolean` | Sim | Indica se houve êxito no fechamento de lote. Onde retorna false se houve erro no fechamento de lote e true se obteve sucesso no mesmo. |
| **`response`** | `SettleRequestResponse` | Não | Objeto de resposta para casos de sucesso no fechamento de lote. Note que em casos de erro, o mesmo não será retornado. (vide [closeBatch()](#closeBatch) ou documentação da classe.) |
| **`error`** | `ErrorData` | Não | Objeto de resposta para casos de erro no fechamento de lote. Note que em casos de sucesso, o mesmo não será retornado. (vide [closeBatch()](#closeBatch) ou documentação da classe.) |

[[Voltar]](./README.md)
