Цель пре-ридинга — понять зачем отделять данные от представления. Около 20 минут.

1. Установить inja для C++: через vcpkg (`vcpkg install inja`) или скачать header `inja.hpp` напрямую (single-header library).
   Также нужен `nlohmann/json`: `vcpkg install nlohmann-json` или `apt install nlohmann-json3-dev`.
2. Прочитать по диагонали:
    - Что такое шаблонизатор: текстовый файл с метками, куда подставляются данные.
    - Jinja2: `{{ variable }}`, `{% for item in list %}`, `{% if condition %}`.
3. Подумать над вопросом: чем шаблонизатор лучше, чем конкатенация строк или `sprintf`?
