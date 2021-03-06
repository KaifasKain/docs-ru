# Active Record Shared Connection

**Примечание:** аналогичный функционал включён в Rails с версии 5.1 (см. [PR](https://github.com/rails/rails/pull/28083)). Поэтому мы крайне не рекомендуем использование `ActiveRecordSharedConnection` в современных версиях фреймворка.

Active Record создаёт отдельное подключение к базе данных для каждого треда. В некоторых случаях мы запускаем больше одного треда в тестах (например, при использовании браузерных тестов с Capybara). Это приводит к тому, что мы не можем использовать транзакционные тесты (`transactional_tests`) для поддержания БД в _чистом_ состоянии.

Долгое время популярным способом решения данной проблемы было использование гема `database_cleaner` в режиме `truncation` или `deletion` для удаления данных после каждого теста. Данный подход работает значительно медленнее, чем использование транзакций, и приводит к увеличению времени выполнения тестов.

Мы предлагаем решить эту проблему по-другому — использовать единое соединение с БД во всех тредах, тем снова делая возможным использование транзакций.

## Инструкция

В вашем `spec_helper.rb` (или `rails_helper.rb`) укажите:

```ruby
require "test_prof/recipes/active_record_shared_connection"
```

Это автоматически активирует режим единого соединения.

Вы можете отключить/включить его, используя следующие методы:

```ruby
TestProf::ActiveRecordSharedConnection.enable!
TestProf::ActiveRecordSharedConnection.disable!
```
