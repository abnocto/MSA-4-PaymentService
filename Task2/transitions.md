# Таблица переходов State Machine

Начальное состояние: STARTED.

Конечные состояния: SUCCEEDED, CANCELED, REFUNDED.

| Исходное состояние | Переходное состояние | Событие |
| :- | :- | :- |
| STARTED | MONEY_AUTHORIZATION_PENDING | Платеж создан |
| STARTED | CANCELED | Не удалось создать платеж |
| MONEY_AUTHORIZATION_PENDING | PAYMENT_AUTO_CHECK_PENDING | Сумма захолдирована |
| MONEY_AUTHORIZATION_PENDING | FINALIZATION_PENDING | Не удалось захолдировать сумму |
| PAYMENT_AUTO_CHECK_PENDING | MONEY_CAPTURE_PENDING | Антифрод и AML одобрили платеж |
| PAYMENT_AUTO_CHECK_PENDING | MONEY_CAPTURE_PENDING | Сработал таймер cut-off: проверки не ответили, запрета нет |
| PAYMENT_AUTO_CHECK_PENDING | MONEY_RELEASE_PENDING | Антифрод или AML отказали |
| PAYMENT_AUTO_CHECK_PENDING | PAYMENT_MANUAL_CHECK_PENDING | Антифрод отправил платеж на ручную проверку, AML не отказал |
| PAYMENT_MANUAL_CHECK_PENDING | MONEY_CAPTURE_PENDING | Оператор одобрил платеж |
| PAYMENT_MANUAL_CHECK_PENDING | MONEY_RELEASE_PENDING | Оператор отказал |
| PAYMENT_MANUAL_CHECK_PENDING | MONEY_RELEASE_PENDING | Не удалось поставить задачу оператору |
| PAYMENT_MANUAL_CHECK_PENDING | MONEY_RELEASE_PENDING | Истекли 20 минут на ручную проверку |
| MONEY_CAPTURE_PENDING | MONEY_TRANSFER_PENDING | Сумма списана на счет платформы |
| MONEY_CAPTURE_PENDING | MONEY_RELEASE_PENDING | Не удалось списать сумму |
| MONEY_TRANSFER_PENDING | FINALIZATION_PENDING | Сумма переведена контрагенту |
| MONEY_TRANSFER_PENDING | MONEY_REFUND_PENDING | Не удалось перевести сумму |
| MONEY_RELEASE_PENDING | FINALIZATION_PENDING | Холд снят |
| MONEY_RELEASE_PENDING | MONEY_RELEASE_STALLED | Не удалось снять холд |
| MONEY_RELEASE_STALLED | MONEY_RELEASE_PENDING | Сработал таймер повторной попытки |
| MONEY_REFUND_PENDING | FINALIZATION_PENDING | Сумма возвращена клиенту |
| MONEY_REFUND_PENDING | MONEY_REFUND_STALLED | Не удалось вернуть сумму |
| MONEY_REFUND_STALLED | MONEY_REFUND_PENDING | Сработал таймер повторной попытки |
| FINALIZATION_PENDING | SUCCEEDED | Платеж переведен в статус "проведен" |
| FINALIZATION_PENDING | CANCELED | Платеж переведен в статус "отклонен" |
| FINALIZATION_PENDING | REFUNDED | Платеж переведен в статус "возвращен" |
| FINALIZATION_PENDING | FINALIZATION_STALLED | Не удалось обновить статус платежа |
| FINALIZATION_STALLED | FINALIZATION_PENDING | Сработал таймер повторной попытки |

## Несколько уточнений

- "Не удалось" значит, что шаг получил бизнес-ошибку, после которой ретраить нет смысла, или исчерпал попытки ретрая.
- FINALIZATION_PENDING - это общее выделенное состояние для шагов уведомлений клиента, службы безопасности, финального обновления статуса платежа.
