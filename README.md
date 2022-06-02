game.py - запускающий игру файл; drones_strategy.py - файл с моделью поведения дрона. Остальные файлы отвечают за
стратегию соперника и написаны не мной.

На данном этапе стоит задача собрать вещество с астероидов на базу в большем объеме, чем противник. При этом чтобы дроны
не летали пустыми более 50% времени.

Дрон может вместить максимум 100 единиц вещества, один астероид содержит от 100 до 200 ед.

Алгоритм полёта дрона:

При рождении (метод `on_born`) дрон направляется к ближайшему астероиду.
https://user-images.githubusercontent.com/80598880/171640897-f454d1d1-74e2-43b9-a8a1-87d4b3ada201.MOV


После возвращения на базу дрон производит разгрузку (метод `on_stop_at_mothership`) и при наличии доступных для полёта астероидов поворачивается к одному из них, чтобы не тратить время после
разгрузки.


Доступные для полёта астероиды генерируются так (метод `current_valid_asteroids`):
каждый астероид проверяется является ли он целью для кого-то из команды (метод `is_any_target`) и не пустой ли он.

По окончании загрузки (метод `on_unload_complete`) следующей целью становится астероид с самой большой загрузкой из половины ближайших
(методы `half_asteroids` и `ast_with_max_payload`). Половина от всех доступных астероидов рассчитывается по медиане всех
расстояний от объекта до астероидов.

При остановке на астероиде (`on_stop_at_asteroid`) дрон параллельно с загрузкой поворачивается в сторону предполагаемой следующей цели:
когда нет доступных для полёта астероидов или свободного места в трюме не больше, чем вещества на астероиде, то поворот
происходит в сторону базы, иначе - к ближайшему астероиду.

Далее возможны следующие варианты:

- если астероид непустой, то происходит загрузка
- если астероид пустой, но дрон непустой, то дрон полетит к ближайшему доступному астероиду.
Если таковых нет, то на базу
- в противном случае происходит проверка все ли астероиды пустые и если да, то дрон останавливается
- когда ни одно из условий не выполняется, то дрон летит на базу
 
Дрон может остановиться на астероиде, если ему не нужно лететь на базу для разгрузки, 
и когда остальные астероиды пусты. Дрон остаётся на базе также когда остальные астероиды либо являются целью
полёта другого дрона из команды, либо они пустые.

После вызова метода `stop` вызывается метод `stater`, который проверяет что все дроны загрузились,
и в этом случае выводит статистику команды.
