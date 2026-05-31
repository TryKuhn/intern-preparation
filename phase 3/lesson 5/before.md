Цель пре-ридинга — прийти с установленным PostgreSQL. Около 25 минут.

1. Установить PostgreSQL и libpqxx:
   ```bash
   sudo apt install postgresql libpq-dev
   sudo apt install libpqxx-dev  # или собрать из исходников
   sudo service postgresql start
   sudo -u postgres psql -c "CREATE USER student WITH PASSWORD 'pass'; CREATE DATABASE practice OWNER student;"
   ```
   Проверка: `psql -U student -d practice -c "SELECT 1;"`.
2. Прочитать по диагонали:
    - Таблицы, строки, столбцы. PRIMARY KEY, FOREIGN KEY.
    - Транзакция: атомарная группа операций. COMMIT/ROLLBACK.
3. Подумать над вопросом: два пользователя одновременно читают баланс счёта (100₽), оба прибавляют 50₽ и сохраняют. Итог: 150₽ вместо 200₽. Как транзакции решают эту проблему?
