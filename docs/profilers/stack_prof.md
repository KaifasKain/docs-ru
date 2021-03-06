# Профилирование тестов со StackProf

[StackProf](https://github.com/tmm1/stackprof) — это профайлер для Ruby, использующий механизм сэмплирования стеков вызовов.

TestProf предоставляет удобный интерфейс для работы со StackProf.

## Инструкция

Установите гем `stackprof` (>=0.2.9):

```ruby
# Gemfile
group :development, :test do
  gem "stackprof", ">= 0.2.9", require: false
end
```

TestProf позволяет запускать StackProf в двух режимах: для всего запуска тестов (`global`) или для индивидуальных тестов (`per-example`).

Для профилирования выполнения всех тестов (от загрузки до завершения) укажите переменную окружения `TEST_STACK_PROF`:

```sh
TEST_STACK_PROF=1 bundle exec rake test

# или для RSpec
TEST_STACK_PROF=1 rspec ...
```

Либо программно в коде:

```ruby
TestProf::StackProf.run
```

Профилирование индивидуальных тестов доступно только для RSpec, так как использует теги.

Добавьте тег `:sprof` для профилирования конкретного примера:

```ruby
it "is doing heavy stuff", :sprof do
  # ...
end
```

**Примечание:** индивидуальное профилирование не работает, если уже активировано глобальное.

## Формат отчёта

StackProf предоставляет CLI для работы с «сырыми» файлами отчётов, в том числе для генерации отчётов в разных форматах.

По умолчанию TestProf в конце прогона тестов показывает команду, которую необходимо выполнить в терминале для формирования отчёта в формате HTML (эту команду необходимо запустить самостоятельно).

Если вы хотите получить отчёт в JSON формате (например, для использования в [speedscope](https://www.speedscope.app)), вам необходимо использовать `stackprof` версии [>=0.2.13](https://github.com/tmm1/stackprof/blob/master/CHANGELOG.md#0213).

Для работы с более старыми версиями вы можете использовать TestProf: укажите переменную окружения `TEST_STACK_PROF_FORMAT=json` или добавьте аналогичную опцию в настройках:

```ruby
TestProf::StackProf.configure do |config|
  config.format = "json"
end
```

## Профилирование загрузки приложения (boot time)

Время загрузки приложения в тестовом окружении также влияет на общее время выполнения тестов. Вы можете профилировать загрузку приложения с помощью StackProf с помощью следующей команды:

```sh
TEST_STACK_PROF=boot rspec ./spec/some_spec.rb
```

**Примечание** Для анализа загрузки приложения мы рекомендуем использовать отчёт в формате флеймграфов.

## Настройка

Вы можете поменять режим работы StackProf с помощью переменной окружения `TEST_STACK_PROF_MODE` (`wall` по умолчанию).

Вы также можете изменить интервал сэмплирования с помощью переменной окружения `TEST_STACK_PROF_INTERVAL`:

- для режимов `wall` и `cpu` — микросекунды (1000 по умолчанию в `stackprof`);
- для режима `object` — число аллокаций (1 по умолчанию `stackprof`).

Все настройки доступны в [stack_prof.rb](https://github.com/test-prof/test-prof/tree/master/lib/test_prof/stack_prof.rb).
