# Проектирование GraphQL API


# Текущие ресурсы
Сейчас есть только get запросы на следующие ресурсы:
- /clients/{id} - Получить информацию о клиенте по ID
- /clients/{id}/documents - Список документов клиента
- /clients/{id}/relatives - Информация о родственниках клиента

## Текущая проблема
Чтобы собрать всю нужную информацию, приходится делать несколько отдельных запросов. Это множит RPS и тормозит клиентские приложения.

## Решение
Переход на GraphQL позволит одним запросом выбрать ровно те поля и вложенные объекты, которые нужны.

### Схема GraphQL
```graphql
type Query {
  client(id: ID!): Client
}

type Client {
  id: ID!
  name: String
  age: Int
  documents: [Document]
  relatives: [Relative]
}

type Document {
  id: ID!
  type: String
  number: String
  issueDate: String
  expiryDate: String
}

type Relative {
  id: ID!
  relationType: String
  name: String
  age: Int
}
```

### Примеры запросов
только базовые данные:
```graphql
query {
  client(id: "123") {
    name
    age
  }
}
```

Базовые + документы:
```graphql
query {
  client(id: "123") {
    name
    documents {
      type
      number
    }
  }
}
```

Всё сразу:
```graphql
query {
  client(id: "123") {
    name
    age
    documents {
      id
      type
    }
    relatives {
      name
      relationType
    }
  }
}
```