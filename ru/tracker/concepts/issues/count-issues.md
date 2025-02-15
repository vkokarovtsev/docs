---
sourcePath: ru/tracker/api-ref/concepts/issues/count-issues.md
---
# Узнать количество задач

Запрос позволяет узнать количество задач, удовлетворяющих условиям вашего запроса.

## Формат запроса {#section_rnm_x4j_p1b}

Перед выполнением запроса [получите доступ к API](../access.md).

Для получения числа задач, удовлетворяющих определенным критериям, используйте HTTP-запрос с методом `POST`. Критерии для поиска передаются в теле запроса в формате JSON:

```json
POST /v2/issues/_count
Host: {{ host }}
Authorization: OAuth <OAuth-токен>
{{ org-id }}

{
"filter": {
    "<имя поля>": "<значение в поле>"
  },
  "query": "фильтр на языке запросов"
}
```

{% include [headings](../../../_includes/tracker/api/headings.md) %}

{% cut "Параметры тела запроса" %}

**Дополнительные параметры**

Параметр | Описание | Формат
----- | ----- | -----
filter | Параметры фильтрации задач. В параметре можно указать название любого поля и значение, по которому будет производиться фильтрация. | Объект
query | Фильтр на [языке запросов](../../user/query-filter.md). | Строка

{% endcut %}

> Запрос количества задач с указанием дополнительных параметров фильтрации:
> 
> - Используется HTTP-метод POST.
> - Ответ должен содержать только количество задач из очереди <q>JUNE</q>, в которых не указан исполнитель.
> 
> ```
> POST /v2/issues/_count HTTP/1.1
> Host: {{ host }}
> Authorization: OAuth <OAuth-токен>
> {{ org-id }}
> 
> {
>   "filter": {
>     "queue": "JUNE",
>     "assignee": "empty()"
>   }
> }
> ```

## Формат ответа {#section_xc3_53j_p1b}

{% list tabs %}

- Запрос выполнен успешно

    {% include [answer-200](../../../_includes/tracker/api/answer-200.md) %}
    
    В ответе содержится число задач, удовлетворяющих условиям вашего запроса.

    ```
    5221186
    ```

- Запрос выполнен с ошибкой

    Если запрос не был успешно обработан, API возвращает ответ с кодом ошибки:

    {% include [answer-error-400](../../../_includes/tracker/api/answer-error-400.md) %}

    {% include [answer-error-401](../../../_includes/tracker/api/answer-error-401.md) %}

    {% include [answer-error-403](../../../_includes/tracker/api/answer-error-403.md) %}

{% endlist %}