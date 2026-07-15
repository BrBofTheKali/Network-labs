<details><summary><b>ansible-collection ecorouter.ecorouteros for CORE router [RUS]</summary>



> ⚠️ <b>Статус коллекции</b>: непубличная — не прикладывается к лабораторной.  

> Стандартные механизмы: `config` и `command` модули.






<details>

<summary><b>🔴 Проблема 1 — Идемпотентность config</b></summary>


 Ложный статус `changed`



Модуль `config` возвращает `changed` на строки, которые уже существуют:


Пример из плейбука:
```json

"updates": [

 "interface e3",

 "isis hello-multiplier 3"

]

```





<b>Некорректная канонизация команд</b>

Регистрирует разницу между `ip add` и `ip address` — считает их разными командами.



</details>





<details>

<summary><b>🟡 Проблема 2 — Чувствительные параметры</b></summary>


Парсер сбивается с настройки. Например `connect port` при присоединении интерфейса должен отсутствовать в конфиге, иначе падает с ошибкой `fatal: already connected`. Требуется ручная проверка в роли или конфиге, благо это реально



</details>





<details>

<summary><b>🟣 Проблема 3 — Сброс настроек</b></summary>



При сбросе некоторых настроек (например, `no interface`) может ломаться механизм перехода в `configure`.



> \*\*Спасает только перезагрузка роутера\*\*



</details>






<details>

<summary><b>🟢 Глобальное решение</b></summary>



Ждать полноценного релиза коллекции или использовать иные модули.



</details>

</details>

